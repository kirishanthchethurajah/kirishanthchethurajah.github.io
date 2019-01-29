---
layout: page
title: Triggering
section: Developing Components
secondsection: Data-Driven Tasks
---

<div class="content2">
<div class="content2-pagetitle">Data-Driven Tasks</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>A data-driven task is a task that wants to perform computations whenever new
data is available on its input ports. In general, data-processing tasks (as for
instance image processing tasks) fall into that category: their goal is to take
data from their input, process it, and push it to their outputs.</p>

<p>Note that in Orocos, port-triggering is a mechanism that can be overlaid on top
of other triggering mechanisms like periodic or file-descriptor triggerings.</p>

<h2 id="declaration">Declaration</h2>
<p>A data-driven task is declared by using the <tt>#port_driven</tt> statement.</p>

<p>Let&rsquo;s look at the following example declaration:</p>

<pre><code class="language-ruby">task_context "Task" do
   input_port  'image', '/Camera/Frame'
   input_port  'parameters', '/SIFT/Parameters'
   output_port 'features' '/SIFT/FeatureSet'

   port_driven 'image'
end
</code></pre>

<p>During runtime, the <tt>updateHook()</tt> method of the corresponding C++ task
will be called when new data arrives on the listed ports (in this case &lsquo;image&rsquo;).
Other input ports are ignored by the triggering mechanism.</p>

<p>Note that, obviously, the listed ports must be input ports. Moreover, they must
be declared <em>before</em> the call to <tt>port_driven</tt>.</p>

<p>Finally, if called without arguments, <tt>port_driven</tt> will activate the
port triggering on all input ports declared before it is called. This means
that, in</p>

<pre><code class="language-ruby">task_context "Task" do
   input_port  'image', '/Camera/Frame'
   input_port  'parameters', '/SIFT/Parameters'
   output_port 'features' '/SIFT/FeatureSet'

   port_driven
end
</code></pre>

<p>both &lsquo;parameters&rsquo; and &lsquo;image&rsquo; are triggering. Now, in</p>

<pre><code class="language-ruby">task_context "Task" do
   input_port  'image', '/Camera/Frame'
   port_driven
   input_port  'parameters', '/SIFT/Parameters'
   output_port 'features' '/SIFT/FeatureSet'
end
</code></pre>

<p>only &lsquo;image&rsquo; is.</p>

<h2 id="c-task-implementation">C++ task implementation</h2>
<p>In the C++ task implementation, the behaviour is slightly different between
version up to RTT 2.X.</p>

<p>In the updateHook(), you will have to read the ports and test if a new data sample arrived. </p>

<pre><code class="language-cpp">Camera::Frame frame;
if(_image.read(frame) == RTT::NewData)
{
}

SIFT::Parameters parameters;
if(_parameters.read(parameters) == RTT::NewData)
{
}
</code></pre>

<h2 id="deployment">Deployment</h2>
<p>As we mentioned at the beginning of this page, the port-triggering mechanism
can be overlaid on top of the other mechanisms.</p>

<p>If one uses the default task declaration</p>

<pre><code class="language-ruby">deployment "test" do
   t = task('Task')
end
</code></pre>

<p>then the deployed task instance will only be triggered when new data is received
on its input ports. It is however possible to use <a href="fd.html">FD-based</a> and
port-based triggering at the same time.</p>

<pre><code class="language-ruby">deployment "test" do
   t = task('Task').
       fd_driven
end
</code></pre>

<p>However, deploying with the <a href="periodic.html">periodic triggering</a> will override
(i.e. disable) the port-triggering.</p>



</div>
</div>
</div>
