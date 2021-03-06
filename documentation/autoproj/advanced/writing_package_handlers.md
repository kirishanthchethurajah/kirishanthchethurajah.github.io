---
layout: page
title: Advanced
section: Build System
secondsection: The manifest.xml file
---

<div class="content2">
<div class="content2-pagetitle">Writing Package Handlers</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>Package handlers are all subclasses of Autobuild::Package. See the autobuild
documentation for more information.</p>

<p>Due to the parallel build feature of autoproj, one must be careful about a few
points when writing new package handlers, or customizing existing ones:</p>

<h2 id="reporting">Reporting</h2>
<p>When writing your own package handlers, or customizing the existing ones, you
must use Autobuild&rsquo;s progress methods to report the build progress.</p>

<p>To report progress about a long-running process, use Package#progress_start</p>

<pre><code class="language-ruby">progress_start "progress message for package %s" do
 # Long-running process.
 # Call Package#progress to display intermediate messages
end
</code></pre>

<p>To display single-shot messages, use Package#message</p>

<pre><code class="language-ruby">message "something that should be noted about package %s"
</code></pre>

<p>If what you are doing is not related to a single package, use the same methods
on Autobuild: Autobuild.progress_start, Autobuild.progress, Autobuild.message</p>

<h2 id="running-subcommands">Running Subcommands</h2>
<p>Always use Autobuild::Subprocess.run to run subcommands. It handles all the
details of diverting the output to a proper log file, handling errors, &hellip;</p>

<pre><code class="language-ruby">Autobuild::Subprocess.run(pkg, 'build', 'command', '--with', '--options', :working_directory =&gt; pkg.srcdir)
</code></pre>

<p>The first argument is either the package for which the command is built, or a
string. The second the build phase (standard ones are
prepare,import,configure,build and install).</p>

<p>The :working_directory option at the end is only useful if you need to run the
command in a specific directory.</p>

<p class="warning">Do <strong>not</strong> use Dir.chdir in a package handler. This will break parallel builds.</p>



</div>
</div>
</div>
