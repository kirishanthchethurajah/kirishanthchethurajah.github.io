---
layout: page
title: oroinspect - finding out what is there
section: Running Components
---
<div class="content2">
<div class="content2-pagetitle">oroinspect - finding out what is there</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>oroinspect is a command line tool that allows you to find out everything that is
oroGen-handled on your system: types, task contexts, deployments.</p>

<p>Call it with</p>

<pre><code class="language-text">oroinspect &lt;pattern&gt;
</code></pre>

<p>and it will display all objects whose name patch &lsquo;pattern&rsquo; (where &lsquo;pattern is a
regular expression)</p>

<p>For instance, on a Rock installation</p>

<pre><code class="language-text">oroinspect hokuyo
</code></pre>

<p>displays</p>

<pre><code>===== hokuyo::Task is a task context defined in hokuyo
------- hokuyo::Task ------
subclass of RTT::TaskContext (the superclass elements are displayed below)
Ports
[out]latency:/hokuyo/Statistics
[out]period:/hokuyo/Statistics
[out]scans:/base/samples/LaserScan
[out]state:/int32_t
[in]timestamps:/base/Time
Properties
end_step:/int32_t: the step at which to end acquisition
merge_count:/int32_t: how much ranges measurement to merge into one single reported measurement
port:/std/string: the device port
rate:/int32_t: the baud rate (only for serial connections)
remission_values:/int32_t: will record remission values if set to 1
scan_skip:/int32_t: how much acquisitions to ignore between two acquisitions to report
start_step:/int32_t: the step at which to start acquisition
test:/hokuyo/Statistics
No attributes
No operations


===== /hokuyo/Statistics is a type defined in hokuyo
/hokuyo/Statistics {
count &lt;/uint32_t&gt;,
max &lt;/uint32_t&gt;,
min &lt;/uint32_t&gt;,
mean &lt;/uint32_t&gt;,
dev &lt;/uint32_t&gt;}

===== /hokuyo/Task_STATES is a type defined in hokuyo
/hokuyo/Task_STATES{
Task_STOPPED = 4,
Task_FATAL_ERROR = 2,
Task_EXCEPTION = 3,
Task_TIMESTAMP_MISMATCH = 7,
Task_RUNTIME_ERROR = 6,
Task_RUNNING = 5,
Task_PRE_OPERATIONAL = 1,
Task_INIT = 0
}
</code></pre>

<p>One can also display only the tasks, deployments and types by calling oroinspect
with --types, --deployments and --tasks</p>



</div>
</div>
</div>
