---
layout: page
title: Configuration files for tasks
section: Setting up a New Project
---
<div class="content2">
<div class="content2-pagetitle">Configuration files for tasks</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p><em>rock-browse</em> or <em>rock-inspect</em> can help you to find existing tasks you might want to include
in your bundle. For example, try <em>rock_inspect -t camera</em> to get a list of
all available tasks associated with &ldquo;camera&rdquo;.</p>

<p>The tasks you include into your bundle are configured using a YAML file.
To create a new configuration file, do:</p>

<pre><code>oroconf extract taskname::Task
</code></pre>

<p>The new template file is generated in the <em>config/orogen</em> subfolder of the bundle,
with a file name that matches the oroGen model name (here,
<em>config/orogen/taskname::Task.yml</em>)</p>


</div>
</div>
</div>
