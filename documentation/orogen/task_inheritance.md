---
layout: page
title: Task Inheritance
section: Developing Components
---
<div class="content2">
<div class="content2-pagetitle">Task Inheritance</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>oroGen understands inheritance between tasks. That means, it is possible to make the
task context inherit from each other, and have the other oroGen features play
well. It also works in a <a href="cross_project.html">cross project fashion</a>.</p>

<p>A base class is defined with</p>

<pre><code class="language-ruby">task_context "SubTask" do
   subclasses "Task"
end
</code></pre>

<p>When inheriting between task contexts, the following constraints will apply:</p>

<ul>
 <li>it is not possible to add a task interface object (port, property, &hellip;) that
has the same name than one defined by the parent model.</li>
 <li>if the parent task is flagged as <a href="task_states.html#needs_configuration">needing
configuration</a>, then the subclass also
does.</li>
 <li>if <a href="task_states.html">extended states</a> are defined, the child class shared
them.</li>
</ul>

<p>Finally, &ldquo;abstract task models&rdquo;, i.e. task models that are used as a base for
others, but which it would be meaningless to deploy since they don&rsquo;t have any
functionality can be marked as abstract with</p>

<pre><code class="language-ruby">task_context "SubTask" do
   abstract
end
</code></pre>



</div>
</div>
</div>
