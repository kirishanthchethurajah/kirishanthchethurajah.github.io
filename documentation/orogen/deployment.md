---
layout: page
title: Deployments
section: Developing Components
---
<div class="content2">
<div class="content2-pagetitle">Deployments</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>In orogen, each deployment is a separate binary (UNIX process) in which a
certain number of tasks have been <em>instanciated</em>. In other words, certain task
contexts classes are tied to a thread and one or multiple triggering mechanisms.</p>

<p>This page will deal with the generics of deployment. The different triggering
mechanisms are developed <a href="triggering/index.html">later in this guide</a>.</p>

<h2 id="the-deployment-block">The deployment block</h2>

<p>Each deployment block declares one binary. For instance:</p>

<pre><code class="language-ruby">deployment "test" do
&lt;deployment statements&gt;
end
</code></pre>

<p>will generate a &ldquo;test&rdquo; binary. By default, it will be installed by CMake. If
that is not desired, for instance if it is for testing purposes only, add the
<tt>do_not_install</tt> statement in the block:</p>

<pre><code class="language-ruby">deployment "test" do
do_not_install
&lt;other deployment statements&gt;
end
</code></pre>

<p>In the block, the following conditions can be tested:</p>

<ul>
<li>the target OS. You can use the <tt>xenomai?</tt> and <tt>gnulinux?</tt>
predicates to enable/disable some parts based on the target OS.</li>
<li>the fact that the binary will be interfaced with CORBA or not. Use the
corba_enabled? predicate to test this. CORBA is the target if the
<tt>--corba</tt> flag is given to orogen during generation, or if the
enable_corba statement is added in the deployment block. It is also possible
to forcefully disable it by adding the disable_corba statement in the
deployment.</li>
</ul>

<h2 id="instanciating-tasks">Instanciating tasks</h2>

<p>The basic thing to be done in a deployment is listing the tasks that should be
instanciated. It is done by using the &lsquo;task&rsquo; statement:</p>

<pre><code class="language-ruby">  task 'TaskName', 'orogen_project::TaskClass'
</code></pre>

<p>It will create a task with the given name and class. By default, that task will
have its own thread.</p>

<h2 id="threading">Threading</h2>

<p>There are mainly two options with respect to threading. In the first case, when
a task is triggered, the associated thread is woken up and the task will be
asynchronously triggered in its own thread when the OS scheduler decides to do
so. It is the safest option (and the default) as the different tasks are made
independent from each other.</p>

<p>In the second case, the task does <em>not</em> have its own thread. Instead, the thread
that triggers it will be used to run the task. In other words, task triggering
stops to be asynchronous, but instead is seen as a method call. This is mainly
useful for tasks that are triggered by data coming in their input port (this
triggering mechanism is described here). The main advantage is that the OS
scheduler is removed from the equation, which can reduce latency.</p>

<p>The first case does not requires a specific declaration in the deployment block.
Nonetheless, the underlying thread can be parametrized with a scheduling class
(realtime/non-realtime) and a priority. By default, a task is non-realtime and
with the lowest priority possible. Changing it is done with the following
statements:</p>

<pre><code class="language-ruby">  task('TaskName', 'orogen_project::TaskClass').
realtime.
priority(&lt;priority value&gt;)
</code></pre>

<p class="warning">Note the dot at the end of the first two lines. It is important and not adding
it would lead to a NoMethodError error during generation.</p>

<p>The priority value being a number between 1 (lowest) and 99 (highest).
Moreover, note that the periodic and IO triggering mechanisms <em>require</em> the task
to be in its own thread.</p>

<p>The second case is called a sequential activity and is declared with:</p>

<pre><code class="language-ruby">  task('TaskName', 'orogen_project::TaskClass').
sequential
</code></pre>

<h2 id="initial-setup-of-the-generated-deployment">Initial setup of the generated deployment</h2>

<p>It is possible to statically specify an initial setup of the task contexts
present in the deployment.</p>

<p>First, it is possible to set simple properties. To do so:</p>

<pre><code class="language-ruby">my_task.property = new_value
</code></pre>

<p>where <tt>new_value</tt> is compatible with the property&rsquo;s type. It works only
for simple types (like numbers or strings), where new_value is the corresponding
ruby literal.</p>

<h2 id="logger-integration">Logger integration</h2>

<p>If you installed orogen&rsquo;s logger component, specific statements are made
available to you in the deployment block.</p>

<p>First, the <tt>add_default_logger</tt> statement would create a logger task
(i) whose name is deploymentname_Logger and (ii) which log file is
deploymentname.log. This convention is used by orocos.rb, for instance, to
implement automated logging setup.</p>

<p>If this default logger does not suit you, you can also add specific loggers
with:</p>

<pre><code class="language-ruby">logger("file name", "task_name").
report(other_task.output_port_name).
report(third_task.another_output_port)
</code></pre>



</div>
</div>
</div>
