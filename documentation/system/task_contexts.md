---
layout: page
title: Task Contexts
section: System Management
secondsection: system
---
<div class="content2">
<div class="content2-pagetitle">Task Contexts</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>When using Syskit, task contexts are directly imported from their oroGen models.
Only task contexts that have been developped using oroGen (i.e. all task
contexts coming from Rock) can currently be integrated in Rock. The general
issue here is that, for the system management layer to work, a model of the
components needs to be available <strong>and</strong> deployed instances of these components
should be described as well.</p>

<p>This page is first going to describe the mapping from oroGen models to Syskit
models. It will then go on with ways in which you can extend these models to add
runtime code, custom configuration code and additional modelling (such as data
services) to existing task contexts.</p>

<h2 id="modelling">Modelling</h2>
<p>The Syskit layer then generates a Ruby class that is a subclass of
<a href="../../Syskit/TaskContext.html">Syskit::TaskContext</a> for each of the oroGen components it finds.
Throught this class, one has access
to all the runtime code execution features of Roby itself. See <a href="../../api/tools/roby">Roby&rsquo;s own
documentation</a> for more information</p>

<p>All task contexts get the following task arguments:</p>

<ul>
  <li><strong>conf</strong> is the configuration that should be applied to the task</li>
  <li><strong>orocos_name</strong> is the name of the deployed orocos task that is used to run
this task context.</li>
</ul>

<p>At runtime, Roby events are used to represent all state transitions except the
one to and from PRE_OPERATIONAL. The RTT TaskContext state transitions are
mapped to the following events:</p>

<ul>
  <li><strong>success</strong>: transition to STOPPED</li>
  <li><strong>exception</strong>: transition to EXCEPTION</li>
  <li><strong>fatal_error</strong>: transition to FATAL_ERROR</li>
  <li><strong>start</strong>: transition to RUNNING</li>
  <li><strong>runtime_error</strong>: transition to RUNTIME_ERROR</li>
</ul>

<p><strong>fatal_error</strong> and <strong>exception</strong> are both forwarded to <strong>failed</strong>.
<strong>runtime_error</strong> is not by default.</p>

<p>Additionally, events are created for each of the <a href="../orogen/task_states.html">custom task
state</a>, with their names converted to
lower_snake_case. If substates are defined (i.e. a custom exception state
defined with exception_states), the corresponding event is forwarded to the
corresponding main state event.</p>

<p>For instance, a task defined with</p>

<pre><code class="language-ruby">name "xsens_imu"
task_context "Task" do
  exception_states :IO_ERROR
end
</code></pre>

<p>will have an <em>io_error</em> event that is forwarded to the <em>exception</em> event.</p>

<h2 id="configuration">Configurations</h2>
<p>Task context configurations, in Syskit, are all managed through <a href="../runtime/configuration.html#configuration-directories">the
configuration directory
mechanisms</a>. The
configuration files are looked for in config/orogen/ and must follow the
model_name.yml convention. For instance, configurations for xsens_imu::Task are
stored in config/orogen/xsens_imu::Task.yml.</p>

<p>The configurations are then selected <a href="workflow.html">when designing subsystems</a> by listing the configuration sections that should be
applied, i.e. the &ldquo;default, fast_sampling&rdquo; configuration will apply the values stored in the
default section and override these values by the fast_sampling section. The way
this selection should be done is detailed in the <a href="subsystem_design.html">subsystem
design</a> section.</p>

<h2 id="extend">Extending the Model</h2>
<p>If a file in models/orogen/ exists, that has the same name than the oroGen
project but with a .rb extension, this file is going to be loaded after the
corresponding oroGen project is loaded and after the subclasses of Roby::Task
have been created. This is commonly used to declare that the task context
provides some service, but can also be used to extend these classes with custom
events, event handlers, &hellip;</p>

<div class="block">
  <p><strong>Generation</strong> template for such a file can be generated for you by Syskit. run</p>

  <pre><code>syskit gen orogen name_of_orogen_project
</code></pre>

  <p>This will create models/orogen/name_of_orogen_project and the class declarations
for each of the task currently existing in this project.</p>
</div>

<p>To extend the task&rsquo;s functionality or models, &ldquo;reopen&rdquo; the task context class to
add some information to it:</p>

<pre><code class="language-ruby">class OroGen::XsensImu::Task
  # Do some declarations there
end
</code></pre>

<p class="note"><strong>Note</strong> the concept of doing &ldquo;class X .. end&rdquo; like this to add something to an
<em>already existing</em> class is called class reopening and is <a href="http://www.ruby-lang.org/en/documentation/quickstart/3/">a standard
feature</a> of
the Ruby programming language.</p>

<p>We&rsquo;ll now see some use for this: how to link <a href="data_services.html">data services</a>
to task contexts and how to add code that is going to be executed at runtime
when the task context is running.</p>

<h2 id="provides">Providing Data Services</h2>
<p>Task contexts can provide data services. However, since they are concrete and
not abstract, their list of ports is predetermined (or almost, we&rsquo;ll see that
later), and therefore the way the &ldquo;provides&rdquo; stanza works is different.</p>

<p>Therefore, when a task context provides a service, it is required that
that the component <em>already</em> has all the ports listed in the service. For
instance, for a task context defined in oroGen with:</p>

<pre><code class="language-ruby">name "xsens_imu"
task_context "Task" do
  output_port "orientation_samples", "base/samples/RigidBodyState"
  ...
end
</code></pre>

<p>then,</p>

<pre><code class="language-ruby">OroGen::XsensImu::Task.provides Rock::Services::Orientation
</code></pre>

<p>will work fine. Errors are generated if no such ports exists <strong>or</strong> if multiple
ports match the specification. If multiple ports match the required port type,
then the system model code tries to match the port name. If an exact match is
found, the ambiguity is considered to be resolved. Otherwise, one has to provide
an explicit port mapping:</p>

<pre><code class="language-ruby">OroGen::XsensImu::Task.provides Rock::Services::Orientation,
  "orientation_samples" =&gt; "port_name_on_the_task"
</code></pre>

<h2 id="extend-with-code">Extending with standard Roby execution features</h2>
<p>Composition models are subclasses of <a href="../../Syskit/TaskContext.html">Syskit::TaskContext</a> which is itself a
grandchild of Roby::Task. As such, much more can be done using the <a href="/api/tools/roby/building/tasks.html">runtime code
execution features of Roby</a>.</p>

<p>For instance, to add a poll handler to the same task context, one would add the following
code:</p>

<pre><code class="language-ruby">class OroGen::XsensImu::Task
  poll do
    # Do some polling
  end
end
</code></pre>

<p>Moreover, one can add, at the Roby level, a method that is going to be called at
component&rsquo;s configuration time before the component&rsquo;s own configureHook gets
called. This feature is commonly used to add task arguments to the model, and
use it to configure the task in cases where a configuration parameter depends on
the general context (i.e. cannot be statically written in a configuration
file). Do not forget to call super there, as the
application of the task&rsquo;s configuration files is done in #configure as well.
For instance:</p>

<pre><code class="language-ruby">class OroGen::CorridorPlanner::Task
  argument :start_point
  argument :target_point

  def configure
    super
    orocos_task.start_point = self.start_point
    orocos_task.target_point = self.target_point
  end
end
</code></pre>



</div>
</div>
</div>
