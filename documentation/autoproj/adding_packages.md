---
layout: page
title: Adding packages
section: Build System
---

<div class="content2">
<div class="content2-pagetitle">Adding packages</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>This is a summary of the ways you can ask autoproj to build a package for you.</p>

<p>The first two ways described are for <strong>local</strong> packages, i.e. packages that you don&rsquo;t share
with the rest of the world. The third one tells you how to share it efficiently
with other people</p>

<h2 id="building-a-new-package">Building a new package</h2>
<p>When you create a new package, and want autoproj to build it, you can simply add
it to the autoproj&rsquo;s main manifest. It will be picked up and built by autoproj.</p>

<p>For instance, if I create a new Rock oroGen driver in</p>

<p>drivers/orogen/new_driver</p>

<p>then I would edit autoproj/manifest and add</p>

<ul>
 <li>drivers/orogen/new_driver</li>
</ul>

<p>to the layout: section.
The next time one calls </p>

<p class="commandline">autoproj build</p>

<p>autoproj will announce that the new
package has been picked up with a message looking like</p>

<pre><code>auto-adding drivers/orogen/new_driver as an oroGen package
</code></pre>

<h2 id="importing-a-package-from-a-vcs">Importing a package from a VCS</h2>
<p>The method above only works with packages that are already checked out. To add a
new package and have autoproj check it out for you, you need to basically do two
things:</p>

<ul>
 <li>declare it in an autobuild file (*.autobuild)</li>
 <li>add VCS information for it (i.e. &ldquo;where to get it&rdquo;)</li>
</ul>

<p>A first step, if you don&rsquo;t want to share that package to a wide audience, is to
add it to the local package set</p>

<ul>
 <li><a href="advanced/autobuild.html">register it</a> in
<tt>autoproj/local.autobuild</tt></li>
 <li><a href="advanced/importers.html">give version control information</a> for it in
<tt>autoproj/overrides.yml</tt></li>
</ul>

<h2 id="sharing-package-definitions-with-the-rest-of-the-world">Sharing package definitions with the rest of the world</h2>

<p>To do that, you will either have to move the package definition to a separate
package set (usually one of those that are in autoproj/remotes/), or to create
your own package set]</p>

<p>For the first solution, you will have to move the package definition from
autoproj/local.autobuild you created in the previous step to an autobuild file
in the package set directory (the file name does not matter as long as it ends
with .autobuild). Moreover, you will have to move the importer definition from
autoproj/overrides.yml to the package set&rsquo;s source.yml file.</p>

<p>For instance, to add a package to the rock.drivers package set in a Rock
installation, one would move the autobuild file to
autoproj/remotes/rock.drivers/ and add the relevant VCS information to
autoproj/remotes/rock.drivers/source.yml</p>

<p>How to create new package sets is <a href="advanced/creating_pkg_set.html">described here</a></p>



</div>
</div>
</div>
