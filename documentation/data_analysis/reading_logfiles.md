---
layout: page
title: Reading Log Data
section: Data Analysis
---
<div class="content2">
<div class="content2-pagetitle">Reading Log Data</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>There are two main ways to read log data. Either converting them to a
space-separated file using the pocolog tool, or by writing Ruby scripts that
leverage the pocolog API.</p>

<h2 id="pocolog-command-line-inspection-of-log-files">pocolog: command-line inspection of log files</h2>
<p>The pocolog command-line tool allows you to easily look at log files.</p>

<p class="commandline">pocolog <em>logfile</em></p>

<p>will list the streams that are present in <em>logfile</em>, along with how many samples they have, and their start and end time. Example:</p>

<pre><code class="language-text">[....1_ImageAcquisition]% pocolog lowlevel.log
File data is in little endian byte order
loading file info from ./lowlevel.idx... invalid index file
building index ...done
Stream controldev.motionCommand [/controldev/MotionCommand]
2503 samples from Fri 14/08/2009 15:01:00
to Fri 14/08/2009 15:02:59 [0:01:59.859]
Stream controldev.rawCommand [/controldev/RawCommand]
2504 samples from Fri 14/08/2009 15:01:00
to Fri 14/08/2009 15:02:59 [0:01:59.859]
Stream hbridge.status [/hbridge/Status]
112657 samples from Fri 14/08/2009 15:01:00
to Fri 14/08/2009 15:02:59 [0:01:59.999]
Stream simpleController.cmd_out [/hbridge/SimpleCommand]
112986 samples from Fri 14/08/2009 15:01:00
to Fri 14/08/2009 15:02:59 [0:01:59.998]
No samples for
can.stats [/can/Statistics]
</code></pre>

<p>The format of each stream description is:</p>

<pre><code class="language-text">Stream stream_name [type_name]
sample_count samples from start_time to end_time [duration]
</code></pre>

<p>As we already mentioned, the generated log files are self-contained. This means that all the information needed to read the data is already contained in the log file. The main information block is a description of each stream&rsquo;s data type.</p>

<p>To read this type definition, use:</p>

<p class="commandline">pocolog <em>logfile</em> -s <em>stream_name</em> --type</p>

<p>It will display the data type for the given stream. Example:</p>

<pre>
[....1_ImageAcquisition]% pocolog lowlevel.log -s
controldev.motionCommand --type
loading file info from ./lowlevel.idx... done
/controldev/MotionCommand { translation &lt;/double&gt;,
rotation &lt;/double&gt; }
</pre>

<h2 id="getting-the-data-as-a-csv-file">Getting the data as a CSV file</h2>

<p>You can ask pocolog to generate a text file with each of the data fields as a column. To do that, simply:</p>

<p class="commandline">pocolog <em>logfile</em> -s <em>stream_name</em></p>

<p>The --from and --to options allow to limit the display to a given index range:</p>

<p class="commandline">pocolog <em>logfile</em> -s <em>stream_name</em> --from 10 --to 50</p>

<p>will skip the first 10 samples and then display 40 samples in a row.
Identically, --every N allows to display one sample every N.</p>

<p>The --time option displays the time of the samples as saved in the log file
(usually, it is the time at which the sample has been written on the file).
More advanced analysis using the Ruby interface</p>

<h2 id="reading-log-files-using-ruby-scripts">Reading log files using Ruby scripts</h2>

<p>The most flexible way is to read the file directly from Ruby. Example code:</p>

<pre><code class="language-ruby">#! /usr/bin/env ruby

require 'pocolog'
include Pocolog

file = Logfiles.new File.open("asguard-controller.log")
data_stream = file.stream("Control.two_phase")
data_stream.samples.each do |realtime, logical, sample|
# Get the position difference between now and the last cycle
# for motor 0
speed = sample.cur[0].position - sample.last[0].position
end
</code></pre>

<p>See <a href="../runtime/ruby_and_types.html">this page</a> for a description of how the C++
types look like on the Ruby side.</p>



</div>
</div>
</div>
