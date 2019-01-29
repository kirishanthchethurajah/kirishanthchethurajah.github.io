---
layout: page
title: Manipulating tasks
section: Running Components
---

<div class="content2">
<div class="content2-pagetitle">Manipulating tasks</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="getting-a-hold-on-task-contexts">Getting a hold on task contexts</h2>

<p>There are three ways to get a TaskContext instance that represents a task context:
using its IOR, name or type. The latter two are accessed using the
Orocos.name_service.</p>

<p><em>Note:</em> the task type can be used only for oroGen-generated tasks. If oroGen is
not used, only the name can be used</p>

<p>As an example, with the following oroGen deployments:</p>

<pre><code class="language-ruby">deployment "lowlevel" do
 task('can', "can::Task").
 task("hbridge", "hbridge::Task")
 add_default_logger
end
</code></pre>

<p>The following code snippet will get a handle on the hbridge task in the three
different ways:</p>

<pre><code class="language-ruby">Orocos.run 'lowlevel' do
 hbridge = Orocos.name_service.get 'hbridge'
 hbridge = Orocos.name_service.get_provides 'hbridge::Task'

 ior = Orocos.name_service.ior 'hbridge'
 hbridge = Orocos::TaskContext.new ior
end
</code></pre>

<p>Note that the type given to the provides option may be a superclass of the
actual returned task. This access method is meant to make startup scripts more
generic. For instance, if we assume that there is a generic <tt>base::IMU</tt>
IMU driver model that is subclassed by <tt>imu::XsensTask</tt> and
<tt>dfkiimu::Task</tt>, then a startup script that does</p>

<pre><code class="language-ruby">imu = Orocos.name_service.get_provides 'base::IMU'
</code></pre>

<p>Will get an IMU task regardless of its name and exact type.</p>

<p>If no task is found or if an ambiguity exists (i.e. if there is more than one
component matching), the method raises Orocos::NotFound.</p>

<h2 id="manipulating-and-monitoring-the-components-state">Manipulating and monitoring the component&rsquo;s state</h2>

<p>The component&rsquo;s state machine can be manipulated using the standard RTT calls.
You need to know the following calls:</p>

<ul>
 <li><em>configure</em>: move the component from the PreOperational to the Stopped
state. In oroGen, only components whose definition include the
<tt>needs_configuration</tt> statement need that step.</li>
 <li><em>start</em>: actually starts the component</li>
 <li><em>stop</em>: stops the component if it is running</li>
</ul>

<p>All these methods are synchronous (i.e. the component is actually started once
the start method returns) and raise StateTransitionFailed if the transition
could not happen.</p>

<p>When the component is in a fatal error state, one can use the
<tt>reset_error</tt> call to get back to the stopped state (where either
<tt>start</tt> or <tt>configure</tt> can be called again).</p>

<p>At runtime, the <tt>ready?</tt>, <tt>running?</tt>, <tt>error?</tt> and
<tt>fatal?</tt> allow to inspect the component&rsquo;s state. Even though it is possible
to access the state directly, avoid to do so unless you really need it. The
reason is that, if oroGen&rsquo;s extended state support is used in a component, then
the above predicates will continue to work while checking for, say, FATAL_ERROR
won&rsquo;t.</p>



</div>
</div>
</div>
