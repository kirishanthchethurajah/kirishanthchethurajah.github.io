---
layout: page
title: Cross-project dependencies
section: Developing Components
---
<div class="content2">
<div class="content2-pagetitle">Cross-project dependencies</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>Each oroGen project can be separated in three things:</p>

<ul>
<li>a <em>type plugin</em> for RTT, that supports the project&rsquo;s defined types in the
RTT, providing marshalling/unmarshalling paths for XML and CORBA for
instance. In RTT terms, this is called the <em>typekit</em>.</li>
<li>a <em>task library</em>, which contains the code for all tasks contexts that the
project defines.</li>
<li>one binary for each defined deployment.</li>
</ul>

<p>In its cross-project support, oroGen brings forward code reuse by allowing to
transparently import and use <em>types</em> and <em>task contexts</em> defined by other
projects.</p>

<h2 id="under-the-hood-the-use-of-pkg-config">Under the hood: the use of pkg-config</h2>
<p>All cross-project dependencies are managed in oroGen by using
<a href="http://www.freedesktop.org/wiki/Software/pkg-config/">pkg-config</a>. This means that oroGen
will generate and install the pkg-config files necessary for it to work, namely
one per parts of the oroGen project (typekit, task library and each deployment), and
install them.</p>

<p>Nonetheless, it also means that these pkg-config files <em>must be available to
pkg-config when you run orogen on a new project</em>.</p>

<p>pkg-config searches for package definitions the standard locations:</p>

<ul>
<li>/usr/local/lib/pkgconfig</li>
<li>/usr/lib/pkgconfig</li>
</ul>

<p>It also searches in the directories listed in the <tt>PKG_CONFIG_PATH</tt>
environment variable. If you install your orogen projects in a non standard
location, you must add this location to <tt>PKG_CONFIG_PATH</tt>. In a bash-like
shell, this is done with:</p>

<pre><code class="language-text">export PKG_CONFIG_PATH=/my/installed/prefix/lib/pkgconfig:$PKG_CONFIG_PATH
</code></pre>

<h2 id="what-is-available-on-my-system-">What is available on my system ?</h2>
<p>One common task is to find out &ldquo;what is already there ?&rdquo;, &ldquo;what is exactly the
definition of type X ?&rdquo; and so on. The <a href="../runtime/oroinspect.html">oroinspect
tool</a> from orocos.rb allows you to find out.</p>

<h2 id="importing-types">Importing types</h2>

<p>To import and define the types defined by another project, just add the
following at toplevel:</p>

<pre><code class="language-ruby">import_types_from "project_name"
</code></pre>

<p>This will import the types in the current project, allowing you to use them in
following task context definitions.</p>

<h2 id="importing-task-context-definitions">Importing task context definitions</h2>

<p>To import task contexts defined by another project, just add the following at
toplevel:</p>

<pre><code class="language-ruby">using_task_library "project_name"
</code></pre>

<p>Once it is done, you can use the imported task contexts in the local project to:</p>

<ul>
<li><a href="task_inheritance.html">subclass them</a></li>
<li><a href="deployment.html">use them in a deployment</a></li>
</ul>

<p class="warning">Note that oroGen will load the project&rsquo;s typekit, since this typekit is required
by the task library itself. You therefore don&rsquo;t need to load the project&rsquo;s
typekit manually.</p>

<h2 id="using_library">Using external libraries which use pkg-config</h2>
<p>If you want to use types from a library that provides a pkg-config file, just
add</p>

<pre><code class="language-ruby">using_library 'base-types'
</code></pre>

<p>where &ldquo;base-types&rdquo; is the name of the installed pkg-config file (see the Rock
base package). The headers from that library will be made available, so you can
do</p>

<pre><code class="language-ruby">import_types_from "base/pose.h"
</code></pre>

<p>where base/pose.h is a header installed by the library.</p>

<h2 id="using-external-libraries-which-do-not-use-pkg-config">Using external libraries which do not use pkg-config</h2>
<p>oroGen provides only support for libraries that use pkg-config. </p>

<p>Hence, if you do <strong>not</strong> want to use pkg-config but use types provided by a library in your task
interface, you will have to modify CMakeLists.txt to look for that library, add
the include and library dirs. Moreover, if needed, you will also have to modify
tasks/CMakeLists.txt to link to that library.</p>

<p>If you do want to use the type provided by this library in your task interface,
you will have to:</p>

<ul>
<li>first, modify the cmake code as described above</li>
<li>second, in the oroGen file, add the relevant include dirs. For instance,</li>
</ul>

<pre><code class="language-ruby">incdir = Set.new
# Write code that detects where the library is and gets
# its include dirs. Add the include dirs in "incdir"
typekit(true).include_dirs |= incdir
</code></pre>



</div>
</div>
</div>
