---
layout: page
title: Task States
section: Developing Components
---

<div class="content2">
<div class="content2-pagetitle">Task States</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>One important aspect of RTT task contexts is that they are not only an
interface. They also have a representation of a state machine.</p>

<h2 id="the-nominal-rtt-state-machine">The nominal RTT state machine</h2>

<div class="img-left-noborder-notopmargin">
  <p><img src="state_machine.png" alt="The default nominal RTT state machine" /> </p>
</div>

<p>On the left is depicted the <strong>nominal</strong> state machine. On each state transition,
the italic names are the names of the method that can make the transition
happen, and the non-italic name the name of the method that will be called on
the component so that it does something, i.e. the ones that you &ndash; the component
developer &ndash; must implement if something is needed for a particular transition.</p>

<p>The configureHook() and startHook() methods can return false, in which case the
transition is refused.</p>

<p>The RuntimeError state is a &ldquo;degraded runtime&rdquo; state. Use it when the component
can still perform its job partially. As an example, a motor driver hardware
could go into RuntimeError in cases where the hardware cannot control the motor
anymore &ndash; for instance because of overheating protections in the hardware &ndash;
but can still read the motor parameters.</p>

<div style="padding-top:2em;"></div>

<h2 id="error-handling-in-the-full-rtt-state-machine">Error handling in the full RTT state machine</h2>

<div class="img-left-noborder-notopmargin">
  <p><img src="error_state_machine.png" alt="The RTT error handling" /> </p>
</div>

<p>Errors are represented in the way depicted on the left. The exception state is
used to represent errors that demand the component to stop, but can be recovered
from by restarting it. The fatal error state, however, is a terminal state:
there is no way to get out of it except restarting the component&rsquo;s process.</p>

<p>RTT will automatically transition from any state to Exception if a C++ exception
is &ldquo;leaked&rdquo; by one of the hooks (i.e. uncaught exception). Because of such a
transition, the stopHook and cleanupHook will be called before getting into
Exception.</p>

<p>If, while going into Exception, another C++ exception is caught, the component
will go into Fatal. In general, there should be no reason to transition to fatal manually.</p>

<h2 id="designing-states-in-orogen-components">Designing states in oroGen components</h2>
<p>There are two points that need to be considered when writing an task context in
oroGen:</p>

<ol>
  <li>from which state should my component start ? It can start from either the
Stopped or PreOperational state <a href="#needs_configuration">more</a>.</li>
  <li>the ability to extend the default state machine by defining new sub-states.
This is a great way to provide a fine-grained interface to monitor a task
context&rsquo;s status <a href="#extended_states">more</a></li>
</ol>

<h2 id="needs_configuration">Components that need configuration</h2>

<p>Task contexts can either start from the Stopped state  or the PreOperational
state. In the first case, only the C++ method startHook() will be called to
start the component. In the second case, botht the configureHook() and
startHook() C++ methods will be called.</p>

<p>As its name implies, the transition between PreOperational and Stopped is meant
to encapsulate the need for complex and/or costly configuration. For instance,
trying to open and configure a device (which can take very long). To give you
another example, in hard realtime contexts, it is expected that startHook() is
hard realtime while configureHook() does not need to be.</p>

<p>If your component needs a configuration step, you will have to tell it to
oroGen. To do that, add the <tt>default_configuration</tt> statement in the task
context block:</p>

<pre><code class="language-ruby">task_context "Task" do
  needs_configuration

  # other definitions
end
</code></pre>

<p>Et voila !</p>

<div class="content-txtbox-warning">
  <p><strong>Removing or adding the <tt>needs_configuration</tt> flag after the first code
generation</strong></p>

  <p>Adding and/or removing this flag actually requires to change the
signature of the constructor of the generated code.</p>

  <p>So, when you change this flag, you need to:</p>

  <ol>
    <li>open templates/task/TaskName.hpp and templates/tasks/TaskName.cpp</li>
    <li>update the constructor declaration in task/TaskName.hpp and task/TaskName.cpp
to match the declaration in the templates</li>
  </ol>
</div>

<h2 id="extended_states">Extending the state machine</h2>

<p>oroGen offers a way to have a more fine-grained reporting mechanism for
components to their coordination (or supervision) layer. This mechanism is based
on the definition of sub-states for each of the runtime and terminal states of
the task context state machine: Running, RunTimeError, Exception and Fatal.</p>

<p>These sub-states are declared in the <tt>task_context</tt> block of the oroGen
specification:</p>

<pre><code class="language-ruby">task_context "MotionTask" do
  # Sub-states of Running (nominal operations)
  runtime_states 'GOING_FORWARD', 'TURNING_LEFT'
  # Sub-states of RunTimeError (degraded functionality)
  error_states 'CANNOT_TURN'
  # Sub-states of Exception (non-nominal end)
  exception_states 'BLOCKED', 'SLIPPING'
  # Sub-states of Fatal (not recoverable error)
  fatal_states 'TOTALLY_BROKEN'
end
</code></pre>

<p>On the C++ side, this mechanism is available through two things:</p>

<ul>
  <li>a States enumeration that defines all the states in a manner that is usable in
the code</li>
  <li>the state(States), error(States), exception(States) and fatal(States) that
allow to declare state changes in the C++ code.</li>
</ul>

<p>For instance, if the updateHook() detects that the system is blocked, it would
do</p>

<pre><code class="language-cpp">void MotionTask::updateHook()
{
    // code
    if (blocked)
    {
        exception(BLOCKED);
        return;
    }
    // code
}
</code></pre>



</div>
</div>
</div>
