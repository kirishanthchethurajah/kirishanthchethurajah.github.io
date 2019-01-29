---
layout: page
title: Data connections
section: Running Components
---
<div class="content2">
<div class="content2-pagetitle">Data connections</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="port-access">Port access</h2>

<p>Getting a port from a task is as easy as</p>

<pre><code class="language-ruby">port_object = my_task.port_name
</code></pre>

<p>The returned port object is either an <a href="../../api/tools/orocos.rb/Orocos/InputPort.html">Orocos::InputPort</a> or an
<a href="../../api/tools/orocos.rb/Orocos/OutputPort.html">Orocos::OutputPort</a>, based on the port type.</p>

<h2 id="creating-connections">Creating connections</h2>

<p>A connection is always created from an output port to an input port:</p>

<pre><code class="language-ruby">output_port.connect_to input_port
</code></pre>

<p>By default, the created connection is a data connection. To change that, one can
use the :type option</p>

<pre><code class="language-ruby">output_port.connect_to input_port, :type =&gt; :buffer, :size =&gt; 10
</code></pre>

<p>Both ports must have the same type, otherwise a ConnectionFailed exception is
raised.</p>

<p>Be reminded, that an input port can be part of multiple connections, i.e. multiple
output ports can be connected to the same input port.</p>

<h2 id="disconnecting">Disconnecting</h2>

<p>There are different ways to disconnect ports</p>

<ul>
<li>one can specify a specific connection to remove:</li>
</ul>

<pre><code class="language-ruby">output_port.disconnect_from input_port
</code></pre>

<ul>
<li>one can completely disconnect an input port</li>
</ul>

<pre><code class="language-ruby">input_port.disconnect_all
</code></pre>

<ul>
<li>one can completely disconnect an output port</li>
</ul>

<pre><code class="language-ruby">output_port.disconnect_all
</code></pre>

<p>Note that it is fine to use the first method even though the process hosting the
input port is dead (e.g. has crashed or cannot be contacted). If it is the
output port that is dead, then one should use the second method.</p>

<h2 id="reading-outputs-or-writing-inputs">Reading outputs or writing inputs</h2>

<p>One can read outputs and write inputs from the Ruby scripts. To achieve this,
one gets a <a href="../../api/tools/orocos.rb/Orocos/OutputReader.html">Orocos::OutputReader</a>
or a <a href="../../api/tools/orocos.rb/Orocos/InputWriter.html">Orocos::InputWriter</a> object
(respectively) with:</p>

<pre><code class="language-ruby">writer = input_port.writer
reader = output_port.reader
</code></pre>

<p>By default, these port access objects will use a data policy for their
connection to the port. To specify a buffer policy, simply do</p>

<pre><code class="language-ruby">writer = input_port.writer :type =&gt; :buffer, :size =&gt; 10
reader = output_port.reader :type =&gt; :buffer, :size =&gt; 10
</code></pre>

<p>Then, the data can be read with</p>

<pre><code class="language-ruby">sample = reader.read
</code></pre>

<p>the returned value will be nil if there is no data available, and the
corresponding Typelib::Type object otherwise. It always return a sample if
the connection has been written at least once.</p>

<p>To get only new samples (i.e. samples that you never read already), do</p>

<pre><code class="language-ruby">sample = reader.read_new
</code></pre>

<p>The returned object allows to manipulate
the C++ data structure as if it was a normal Ruby object. For instance, a data
sample looking like</p>

<pre><code class="language-cpp">struct LaserReadings
{
  std::string name;
  base::Time time;
  std::vector&lt;double&gt; readings;
};
</code></pre>

<p>One can do</p>

<pre><code class="language-ruby">sample.name # returns a Ruby string
sample.time # returns a Typelib object that has .seconds and .microseconds methods
sample.readings # returns an Enumerable of floating-point values
</code></pre>

<p>and written with</p>

<pre><code class="language-ruby">sample = writer.new_sample
sample.name = "MyReading"
sample.time.seconds = Time.now.tv_sec
sample.time.microseconds = Time.now.tv_usec
writer.write(sample)
</code></pre>


</div>
</div>
</div>
