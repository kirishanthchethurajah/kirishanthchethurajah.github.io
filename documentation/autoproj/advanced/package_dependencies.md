---
layout: page
title: Advanced
section: Build System
secondsection: Package Dependencies
---

<div class="content2">
<div class="content2-pagetitle">Package Dependencies</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>There are two ways to specify dependencies between packages. In order of
&ldquo;general use&rdquo;:</p>

<ul>
<li>list them in the package&rsquo;s manifest.xml</li>
<li>add them in the package set&rsquo;s autobuild scripts</li>
</ul>

<p>Moreover, a dependency can either be mandatory or optional.</p>

<p>In the first case, autoproj will always refuse to build a package if one of its
dependencies is either not defined or <a href="../customization.html#exclude_packages">excluded from the build</a></p>

<p>In the second case, autoproj will only take into account the dependency
information for building, and simply ignore the dependency if the depended-upon
package is inexistent or excluded from the build.</p>

<p>The rest of this page details how to add these two types of dependencies</p>

<h2 id="when-to-make-a-dependency-optional">When to make a dependency optional</h2>

<p>This is a matter of engineering, really. Concrete examples are:</p>

<ul>
<li>libraries that contain both core functionality and GUIs. GUIs won&rsquo;t get built
on the embedded systems (a.k.a. &ldquo;robots&rdquo;), and therefore should be made
optional</li>
<li>dependencies on &ldquo;heavy&rdquo; stuff, when the functionality that depends on these
heavy packages is not the main package functionality.</li>
</ul>

<h2 id="dependencies-in-the-packages-manifestxml">Dependencies in the package&rsquo;s manifest.xml</h2>

<p>This should be the main way through which you specify dependencies</p>

<p>The <a href="manifest-xml.html">manifest.xml file</a> is a file that lies in each package&rsquo;s
root directory. It contains general information (description, author, license,
&hellip;) and build related information</p>

<p>The <tt><depend></depend></tt> tag can be used to specify dependencies between
packages. For instance, to add a dependency on the &ldquo;drivers/xsens_imu&rdquo; package, one does</p>

<pre><code>&lt;depend name="drivers/xsens_imu" /&gt;
</code></pre>

<p>Making a dependency optional is simply a matter of adding the
<tt>optional=&rdquo;1&rdquo;</tt> flag:</p>

<pre><code>&lt;depend name="drivers/xsens_imu" optional="1" /&gt;
</code></pre>

<h2 id="dependencies-in-autobuild-files">Dependencies in autobuild files</h2>

<p>It is useful when some dependencies should be added or not depending on some
[configuration options](creating_pkg_set.html#configopts}</p>

<p>This is simply a matter of calling #depends_on(package_name) or
#optional_dependency(package_name) on the package objects.</p>

<p>For instance, when defining a new package</p>

<pre><code class="language-ruby">cmake_package 'drivers/orogen/pkgname' do |pkg|
if user_config('WITH_GUI')
  pkg.depends_on 'gui'
end
end
</code></pre>

<p>Or later in for instance autoproj/overrides.rb</p>

<pre><code class="language-ruby">if user_config('WITH_GUI')
package('drivers/orogen/pkgname').depends_on 'gui'
end
</code></pre>



</div>
</div>
</div>
