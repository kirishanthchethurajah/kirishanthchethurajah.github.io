---
layout: page
title: Triggering
section: Developing Components
secondsection: Periodic Triggering
---
<div class="content2">
<div class="content2-pagetitle">Periodic Triggering</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>This is the most simple triggering method. When a task is declared periodic, the
task context&rsquo;s <tt>updateHook()</tt> method will be called with a fixed time
period.</p>

<h2 id="declaration">Declaration</h2>

<p>To do this, simply add the <tt>periodic(period)</tt> statement to the task
declaration in your deployments:</p>

<pre><code class="language-ruby">deployment "example" do
task('Task').
  periodic(0.01)
end
</code></pre>

<p>The period is given in seconds. Deploying with a periodic triggering disables
<a href="ports.html">port-based triggers</a> as well as <a href="fd.html">fd-based triggers</a></p>

<h2 id="c-task-implementation">C++ task implementation</h2>

<p>This triggering mechanism does not require any specific change to the C++ task
implementation. </p>

<p>Nonetheless, if a periodic activity is used, then
<tt>TaskContext::getPeriod()</tt> will return the period in seconds.</p>



</div>
</div>
</div>
