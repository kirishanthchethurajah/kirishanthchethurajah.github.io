---
layout: page
title: Debugging Components
section: Running Components
---
<div class="content2">
<div class="content2-pagetitle">Debugging Components</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>When starting oroGen-generated deployments, the Orocos.run command gives you
some ways to use common debugging tools like gdb or valgrind to debug your
components.</p>

<h2 id="gdb">GDB</h2>

<p>Orocos.run can be told to start the processes / deployments under gdb using the
:gdb option</p>

<pre><code class="language-ruby">Orocos.run 'my_project::Task' =&gt; 'task', :gdb =&gt; true do
...
end
</code></pre>

<p>This starts the process using the gdb server. Once the process is started,
Orocos.run warns you that</p>

<pre><code>Orocos[WARN]: process task has been started under gdbserver, port=30001. The components will not be functional until you attach a GDB to the started server
Process /media/Data/dev/rock/master-full/install/bin/orogen_default_camera_firewire__CameraTask created; pid = 29756
</code></pre>

<p>You have then to attach a gdb client to it with</p>

<pre><code>gdb /media/Data/dev/rock/master-full/install/bin/orogen_default_camera_firewire__CameraTask
&gt; target remote localhost:30001
</code></pre>

<p>Note that if you need to start some components under gdb and some not, list the
components / deployments you want to start under gdb instead of &lsquo;true&rsquo;</p>

<pre><code class="language-ruby">Orocos.run 'my_project::Task' =&gt; 'task', 'my_deployment', :gdb =&gt; ['task', 'my_deployment'] do
...
end
</code></pre>

<h2 id="valgrind">Valgrind</h2>
<p>With the same general interface, you can start the components on valgrind</p>

<pre><code class="language-ruby">Orocos.run 'my_project::Task' =&gt; 'task', :valgrind =&gt; true do
...
end
</code></pre>

<p>or, if you want to select specific processes:</p>

<pre><code class="language-ruby">Orocos.run 'my_project::Task' =&gt; 'task', 'my_deployment',
 :valgrind =&gt; ['task', 'my_deployment'] do
...
end
</code></pre>

<p>In order to not get spammed by the valgrind output, you definitely should setup
the output redirection to file:</p>

<pre><code class="language-ruby">Orocos.run 'my_project::Task' =&gt; 'task', 'my_deployment',
 :output =&gt; '%m-%p.log', :valgrind =&gt; ['task', 'my_deployment'] do
...
end
</code></pre>

<p>in which case the valgrind output is redirected to a separate process logfile,
which is the process&rsquo; log file name with .valgrind appended (in the example
above, task-PID.log.valgrind and my_deployment-PID.log.valgrind, where PID is
the process ID of the started deployment)</p>

<p>It is also possible to pass options to valgrind:</p>

<pre><code class="language-ruby">Orocos.run 'my_project::Task' =&gt; 'task', 'my_deployment',
 :valgrind: true, :valgrind_options: ['--undef-value-errors=no'] do
...
end
</code></pre>

<h2 id="ignore-rtt-exception-handling">Ignore RTT Exception Handling</h2>
<p>RTT catches by default all exeption from the tasks.
This is because RTT tries to be as safe as possible.
A somehow crashed task should never influence the execution of
other components within the same deployment. However, for debugging
purposes, it is convenient to let the exceptions be unhandled and
cause a process abort. This makes the debugging with gdb easier.
To activate this <strong>debugging</strong> behaviour set the environment
variable RTT_IGNORE_EXCEPTION to 1.</p>

<pre><code>    RTT_IGNORE_EXCEPTION=1 ruby any_script.rb
</code></pre>


</div>
</div>
</div>
