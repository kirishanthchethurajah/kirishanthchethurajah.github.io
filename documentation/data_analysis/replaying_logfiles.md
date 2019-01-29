---
layout: page
title: Replay Log Data
section: Data Analysis
---
<div class="content2">
<div class="content2-pagetitle">Replay Log Data</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>Orocos::Log::Replay is a Ruby class which eases common tasks on log files. It
provides methods to access, filter and replay the data inside Ruby scripts, or
into oroGen components. </p>

<p class="note"><strong>Note:</strong> See also <a href="../graphical_user_interface/300_displaying_logfiles.html">Displaying Log Files</a> if you want to visualize the logged samples.</p>

<h2 id="features">Features</h2>

<p>The Replay class provides a range of features: </p>

<ul>
<li>log file loading</li>
<li>time synchronous replay</li>
<li>step by step replay</li>
<li>seeking</li>
<li>user-specific log data filtering</li>
<li>log file abstraction, i.e. in Ruby scripts, log files behave like instances of Orocos::TaskContext</li>
</ul>

<p class="warning">This functionality requires the orocos.rb package to be installed, which is
contained in the Rock toolchain.</p>

<p class="notes">See <a href="../runtime/ruby_and_types.html">this page</a> for a description of the mapping
from the C++ types in Ruby </p>

<h2 id="usage">Usage</h2>
<p>The replayed log files can either be specified when initializing a
Orocos::Log::Replay object, or later on by using the #load method.</p>

<pre><code class="language-ruby">replay = Log::Replay.open(file0,folder0,...)
replay.load(folder1)
replay.load(file1)
</code></pre>

<p class="warning">So far it is not possible to load multiple log files from the same component at the
same time because of overlapping names.</p>

<p>After loading, the class offers an interface to log files that mimicks the
Orocos::TaskContext API. This assumes that the log streams are called
&lsquo;task_name.port_name&rsquo; &ndash; which is the case if you are using the
Orocos.log_all_ports setup method. </p>

<p>To access all streams from a particular task context, do</p>

<pre><code class="language-ruby">task1 = replay.camera
task2 = replay.task('camera')
</code></pre>

<p>The equivalent call for live components is Orocos.name_service.get</p>

<p>Then, each log stream is mapped to a port-like object:</p>

<pre><code class="language-ruby"># Accesses the data logged from the port 'frame' of the
# task 'camera'
output_port = replay.camera.frame
</code></pre>

<p>They are accessed through a reader or directly via the method read.
The only difference between the two ways is the method &lsquo;read&rsquo; will always return the last replayed
data sample, while the behavior of the reader depends on the applied connection policy.</p>

<pre><code class="language-ruby">reader = output_port.reader :type =&gt; :data
puts reader.read                    #no different between both
puts output_port.read               #reads
</code></pre>

<pre><code class="language-ruby">reader = output_port.reader :type =&gt; :buffer,:size =&gt; 1
puts reader.read                    #=&gt; last replayed sample
puts reader.read                    #=&gt; nil
puts output_port.read               #=&gt; last replayed sample
puts output_port.read               #=&gt; last replayed sample
</code></pre>

<p>If you wish to feed logged data into a running RTT component, you can use the
normal connect_to method:</p>

<pre><code class="language-ruby">replay.camera.frame.connect_to rtt_task.frame
</code></pre>

<p>If you wish to transform the data before it gets fed to the task, you can
specify a filtering block:</p>

<pre><code class="language-ruby">replay.camera.frame.connect_to rtt_task.frame do |sample|
# the sample gets transformed here
sample # return the modified value
end
</code></pre>

<p>Moreover, to make simple setups straightforward, the Replay class can autoconnect
log streams to component ports. For each port of
the target task, an equivalent port (same name and same type) is searched for in
the logged data. If the name differs, a name mapping can be provided.</p>

<pre><code class="language-ruby"># performs an auto connect
replay.connect_to rtt_task
</code></pre>

<pre><code class="language-ruby"># Connects matching ports between the log data and a running
# RTT component
# When looking for matching names, 'frame' (on logged
# side) will be replaced by "iframe" (on component side).
replay.connect_to rtt_task, :frame =&gt; :iframe
</code></pre>

<p>Once the replay network is properly set up, you start replaying by calling #run#.</p>

<pre><code class="language-ruby">replay.run         # replay all logged data as fast as possible
replay.run(true,1) # replay as fast as the data were recorded
replay.step        # replay one data sample
</code></pre>

<div class="warning">
<p>Only ports are replayed which are connected to RTT components, for which a
reader has been created or a filter was specified. If you want to specify that
one particular stream should be replayed, set its #tracked# property to true:</p>

<pre><code class="language-ruby"># Forces the log stream to be replayed
replay.camera.frame.tracked = true
</code></pre>
</div>

<h2 id="time">Understanding time in log files</h2>
<p>Rock&rsquo;s log file format embeds two times per sample one <em>logical time</em> and one <em>real
time</em>. Currently, these two times correspond to the time at which the logger
received a sample (logical time) and the time it wrote it to disk (real time).</p>

<p>However, when visualizing or replaying data, one usually wants to consider the
time of the sample as the replay time (i.e. the time stored in the sample&rsquo;s
timestamp field, if there is one). In most cases, the logical time will do
the trick. However, it won&rsquo;t be the case if the computer on the robot is
overloaded (either because the disk is too slow or the CPU load too high).</p>

<p>In the future, the logical time will be the sample time. However, the current
implementation of Rock&rsquo;s logger component does not allow that, so one needs to
use some options on Rock&rsquo;s log replay tooling.</p>

<p>The following methods are limited for data types that have a &lsquo;time&rsquo; field of
type /base/Time. The replay algorithm will fall back to the logical time for
streams do not fit this.</p>

<p>On the command line, one can use the --time-source option to rock-replay:</p>

<pre><code>rock-replay --use-sample-time mylogfile.0.log
</code></pre>

<p>In Ruby scripts, one can use the time_source attribute on the Replay class</p>

<pre><code class="language-ruby">replay = Log::Replay.open(file0,folder0,...)
replay.use_sample_time = true
</code></pre>

<p>Finally, one can process log files offline to store the sample time in place of the
logical time <a href="converting_logfiles.html">using rock-convert</a>. This is very useful
when the same logfile is going to be processed multiple times, as it will
remove the need to add the use_sample_time option to the other tools</p>

<pre><code>rock-convert --use-sample-time LOGFILE
</code></pre>

<h2 id="example-loading-and-accessing-log-data">Example: Loading and accessing log data:</h2>

<pre><code class="language-ruby">#!/usr/bin/env ruby

require 'orocos/log'
include Orocos

replay = Log::Replay.open('camera.0.log')
replay.camera.frame do |frame|
puts frame.time
end

#replay as fast as possible
replay.run
</code></pre>

<h2 id="example-loading-and-replaying-log-data">Example: Loading and replaying log data</h2>

<pre><code class="language-ruby">#!/usr/bin/env ruby

require 'orocos/log'
include Orocos

replay = Log::Replay.open('camera.0.log')

Orocos.run 'deployment_x' do
task_x = Orocos.name_service.get('task_x')

#auto connect
replay.connect_to task_x
task_x.start
replay.run
end
</code></pre>

<h2 id="example-filtering-log-data">Example: Filtering log data</h2>

<pre><code class="language-ruby">#!/usr/bin/env ruby

require 'orocos/log'
include Orocos

replay = Log::Replay.open('camera.0.log')

Orocos.run 'deployment_x' do
task_x = Orocos.name_service.get('task_x')
replay.connect_to task_x

#the filter is applied on each frame
#before it is written to ports or readers

replay.camera.frame.filter = Proc.new do |frame|
  frame.time = Time.now
  frame
end

task_x.start
replay.run
end
</code></pre>

<h2 id="example-advanced">Example: Advanced</h2>

<pre><code class="language-ruby">#!/usr/bin/env ruby

require 'orocos/log'
include Orocos

replay = Log::Replay.open('camera.0.log','sonar.0.log')

Orocos.run 'deployment_x','deployment_y' do
task_x = Orocos.name_service.get('task_x')
task_y = Orocos.name_service.get('task_y')
replay.connect_to task_x
#port mapping
replay.connect_to task_y, "sonar.frame" =&gt; "iframe"

#get a reader
#Be careful. Here is a filter applied on the reader
#if the original frame is changed in the filter all
#readers for the same frame which are created after this
#reader are affected as well.
reader = replay.camera.frame.reader do |frame|
  puts frame.time
  frame
end

#display all ports of type /base/samples/frame/Frame
ports = replay.find_all_ports('/base/samples/frame/Frame')
ports.each do |port|
  port.pp
end

task_x.start
task_y.start

replay.step(false)                    #replay one step
replay.step(false)                    #replay one step

puts replay.camera.frame.read.time    #display current
                                      #frame time stamp
puts reader.read.time                 #should be the same
replay.rewind                         #rewind log data

#the code block is called for each message on all
#ports
replay.run do |port,data|
  puts data.time if port.name == 'frame'
do
end
</code></pre>


</div>
</div>
</div>
