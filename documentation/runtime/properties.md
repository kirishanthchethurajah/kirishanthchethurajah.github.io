---
layout: page
title: Properties
section: Running Components
---
<div class="content2">
<div class="content2-pagetitle">Properties</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>As for ports, properties are accessed with task#property_name and
task#property_name=. For instance, the &ldquo;port&rdquo; property of the hokuyo task is
read with</p>

<pre><code class="language-ruby">task = Orocos.name_service.get 'hokuyo'
puts task.port
</code></pre>

<p>and written with</p>

<pre><code class="language-ruby">task = Orocos.name_service.get 'hokuyo'
task.port = "/dev/ttyACM0"
</code></pre>

<p>A VERY important note is that the value returned by reading a property is a
<em>copy</em> of the property&rsquo;s value. In practice, it means that, for structures,</p>

<pre><code class="language-ruby">task.config.value = 10
</code></pre>

<p>will change the field &ldquo;value&rdquo; of the <em>copy</em> of the property to 10. Not the
property itself. (<strong>Note</strong> using a property in this manner will soon be
forbidden)</p>

<p>The preferred way to update complex properties in Ruby is to use a block form</p>

<pre><code class="language-ruby">task.config do |p|
p.value = 10
end
</code></pre>



</div>
</div>
</div>
