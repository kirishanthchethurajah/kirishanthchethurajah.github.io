---
layout: page
title: Add build system to git
section: Setting up a New Project
---
<div class="content2">
<div class="content2-pagetitle">Add build system to git</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>Pick a git hosting method and create a new project, containing two
repositories.  One of them is usually named <em>buildconf.git</em> - it is for your
the main autoproj configuration. The other one should be named
<em>package_set.git</em> - it describes the software collection of packages used in
your project. Push your build system&rsquo;s current <em>autoproj</em> folder to
<em>buildconf.git</em> on your git hoster.</p>

<p>To get a feeling have a look at <code>rock-core</code>s
<a href="https://github.com/rock-core/buildconf">buildconf</a> and a
<a href="https://github.com/rock-core/rock-package_set">rock-package_set</a></p>

<p>In the main folder of your new build system (<em>acd your_project_name</em>), do:</p>

<pre><code>autoproj switch-config git@your_git_hoster:your_project/buildconf.git
</code></pre>

<p>To add the package set to git version control, first create an empty
<em>package_set</em> folder. Create a <em>source.yml</em> file and edit the first line as
follows:</p>

<pre><code>name: your project name
</code></pre>

<p>Then do:</p>

<pre><code>git init
touch libs.autobuild
touch orogen.autobuild
touch deps.osdeps
git add .
git commit -m "Initial commit"
git push git@your_git_hoster:your_project/package_set.git HEAD:master
</code></pre>

<p>Choose the packages you need for your bundle from <a href="../../package_directory.html">the package
directory</a> and update the <em>autoproj/manifest</em>
file accordingly. For more information about how to install packages, have a
look <a href="../tutorials/190_installing_packages.html">here</a>.</p>

<p>In short, the desired packages need to be added to the <em>layout</em> section of your
<em>autoproj/manifest</em> file, for example - for a Hokuyo laser scanner:</p>

<pre><code>layout:
  - drivers/hokuyo
</code></pre>

<p>Make sure that the corresponding package set is also declared in your
<em>autoproj/manifest</em> file. For the a.-m. example, it is:</p>

<pre><code>package_sets:
  - github: rock-core/package_set
</code></pre>

<p>Call <em>aup &ndash;all</em> to install the declared packages.</p>


</div>
</div>
</div>
