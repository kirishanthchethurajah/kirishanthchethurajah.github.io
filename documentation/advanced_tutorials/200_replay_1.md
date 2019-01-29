---
layout: page
title: Replaying data in scripts
section: Advanced
---
<div class="content2">

<div class="content2-pagetitle">Replaying data in scripts</div>

<div class="content2-container line-box">
<div class="content2-container-1col">



<p><em>Data replay</em> is the ability to take data from log files and write it back into
live components. This is a very convenient ability if you want to test your
algorithm with a specific data set, to test on synthetic data sets, or if the
message producer is not available all the time. </p>

<p>In this tutorial, we subsistute the message producer of the basics tutorial by a
log file which was previously on the &ldquo;live&rdquo; system.</p>

<p>The basic workflow is to get a logfile context using Orocos::Log::Replay and
then access the log streams as if they were tasks and ports:</p>

<pre><code class="language-ruby">#library for displaying data
require 'vizkit'

#load log file
log = Orocos::Log::Replay.open("message_producer.0.log")

#now you can access all logged data by
#addressing them by their task and port name
#log.task_name.port_name

#start deployment
Orocos.run 'messages::Consumer' =&gt; 'message_consumer' do
   message_consumer = Orocos.name_service.get "message_consumer"

   log.message_producer.messages.connect_to message_consumer.messages

   #still needed since we added "needs_configuration"
   #in the orogen task definition
   message_consumer.configure
   message_consumer.start

   #open control widget and start replay
   Vizkit.control log
   Vizkit.exec
end
</code></pre>

<p>If, instead of pushing the data to a component, you want to process it in Ruby,
you can redirect it to code blocks:</p>

<pre><code class="language-ruby">require 'vizkit'

log = Orocos::Log::Replay.open("message_producer.0.log")
Orocos.run 'messages::Consumer' =&gt; 'message_consumer' do
   message_consumer = Orocos.name_service.get "message_consumer"

   log.message_producer.messages.connect_to message_consumer.messages

   message_consumer.configure
   message_consumer.start

   # Redirect data to a code block
   log.message_producer.messages.on_data do |sample|
       puts sample.time
   end

   Vizkit.control log
   Vizkit.exec
end
</code></pre>

<p>The dataflow of the replay mechanism looks like:</p>

<p class="align-center"><img src="200_log_replay.png" alt="Log Replay Dataflow" /></p>


</div>
</div>
</div>
