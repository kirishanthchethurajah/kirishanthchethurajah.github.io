---
layout: page
title: Introduction
section: Data Analysis
---
<div class="content2">
<div class="content2-pagetitle">Overview</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="data-analysis">Data Analysis</h2>

<p>This part of the Robot Construction Kit documentation covers the topic of data
analysis, which is supported by a Rock using a dedicated tools. These tools
allow you to:</p>

<ul>
<li><strong>log your data</strong>. Logging data is cheap (CPU-wise) in Rock, and can
mostly always been kept turned on at all times without a great impact on the
system performance (unless you have I/O problems, obviously). The generated
log files are <strong>self contained</strong>, which means that the only thing you need
to read them are the log tools. No other software is required &ndash; i.e. there
is no need for the modules that generated them to be kept around.</li>
<li><strong>replay</strong> logged data using Ruby scripting, with an API that is as
close as normal component deployment as possible.</li>
<li><strong>display</strong> both logged or live data in a uniform manner</li>
</ul>

<p>People using robotic systems know that logging is the root of most of the work. Why ? Usually you will analyze your logged data post-mortem.
But because a robot software layer generates a lot of data you require tools to handle the data, so that you can reproduce problems and find the information you are really looking for.
Thus, being able to replay data so that you can reproduce problems or just test different algorithms on the same data is an important aspect of your work.
Eventually, having the means to display your data will improve your <em>online</em> and <em>offline</em> experiences, since you can better estimate the system status and reduce the time needed for troubleshooting.</p>


</div>
</div>
</div>
