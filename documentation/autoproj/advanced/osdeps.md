---
layout: page
title: Advanced
section: Build System
secondsection: Operating System dependencies
---
<div class="content2">
<div class="content2-pagetitle">Operating System dependencies</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>Autoproj offers the possibility to use OS-packaged software instead of building
it, to leverage the underlying platform&rsquo;s software. This page details how it&rsquo;s
done.</p>

<h2 id="defining-dependencies-between-source-packages-and-os-packages">Defining dependencies between source packages and OS packages</h2>

<p>If a source package depends on an OS package, this dependency should be declared
in the same way than between  source packages:</p>

<ul>
 <li>either by adding the <depend package="os_dep_name"></depend> tag in the
manifest.xml, or</li>
 <li>by calling #depends_on(&ldquo;os_dep_name&rdquo;) in the autobuild file.</li>
</ul>

<p>The os_dep_name above being the name of the package as declared in the OS
dependencies files defined below.</p>

<p>During the build, autoproj looks first for OS dependencies. If no dependency is
available for that particular platform, it will then look for a source package
definition and build it. If none of the two exists, an error is returned.</p>

<h2 id="os-packages">OS packages</h2>

<p>The general format of the an OS package definition:</p>

<pre><code>name:
   distribution1,distribution2:
       version1,version2: [package_name1, package_name2]
</code></pre>

<p>Where &lsquo;name&rsquo; is the name used to declare the dependency (see above), <code>distribution</code>
the name of the distribution and <tt>version</tt> the
distribution&rsquo;s version, and <tt>package_name</tt> the name of the package in
the underlying OS.</p>

<p>Since the osdeps file is a YAML file, it could also be written</p>

<pre><code>name:
   distribution1,distribution2:
       version1,version2:
           - package_name1
           - package_name2
</code></pre>

<p>If only one package needs to be installed, one can use the shortcut</p>

<pre><code>name:
   distribution1,distribution2:
       version1,version2: package_name
</code></pre>

<p>Finally, if the package name is version-independent, the version can be omitted:</p>

<pre><code>name:
   distribution1,distribution2: package_name
</code></pre>

<p>Examples:</p>

<pre><code>ruby:
   debian,ubuntu:
       9.04,10.04,sid: libruby-dev
boost:
   debian:
       - libboost1.38-dev
       - libboost-program1.38-dev
   ubuntu:
       9.04,10.04: libboost-dev
</code></pre>

<p>At the time of this writing, autoproj is able to install packages on
Ubuntu/Debian and Gentoo. Support for other operating systems can be easily
added, so <a href="http://github.com/doudou">contact me</a> if you want to do so.</p>

<h2 id="rubygems-packages">RubyGems packages</h2>

<p>RubyGems packages are OS-independent. In the osdeps files, they can be referred
to by replacing the OS distribution name by &lsquo;gem&rsquo;.</p>

<p>Example:</p>

<pre><code>hoe:
   gem:
       hoe
</code></pre>

<p>If the OS dep name and the RubyGems name are the same, one can use the shortcut</p>

<pre><code>hoe: gem
</code></pre>

<p>Note that it is possible to use a mixture of RubyGems and OS packages. For
instance, the following snippet will both install the gnuplot package and the
gnuplot RubyGems whenever an osdep on &lsquo;gnuplot&rsquo; is declared.</p>

<pre><code>gnuplot:
   gem: gnuplot
   debian: gnuplot
</code></pre>

<h2 id="ignoring-some-dependencies">Ignoring some dependencies</h2>
<p>It is possible that, on some operating systems, a given package should simply be
ignored. To do so, simply use the &lsquo;ignore&rsquo; keyword. Example:</p>

<pre><code>gnuplot:
   gem: gnuplot
   debian: ignore
   ubuntu: gnuplot
</code></pre>

<h2 id="relationship-between-source-packages-and-osdeps-packages">Relationship between source packages and osdeps packages</h2>

<p>In autoproj, osdeps and source (&ldquo;normal&rdquo;) packages are seen the same way.</p>

<p>However, since installing the OS package is less work (and much faster) than
building it ourselves, the following prioritization exists:</p>

<ul>
 <li>if both a normal autoproj package and an osdeps package exist, the osdeps
package takes precedence.</li>
 <li>on OSes where no osdeps package exist, the autoproj package will be built
instead</li>
</ul>

<p>This default scheme requires the source package and the osdeps package to have
the same name. Moreover, one might want on some occasions to build the source
package (for instance because it is slightly more up-to-date).</p>

<p>This source package / osdeps package relationship can be fine tuned using
Autoproj.add_osdeps_overrides. This is usually done either locally in
autoproj/overrides.rb, or on a package set basis in the package set&rsquo;s
overrides.rb.</p>

<p>A different name can be given for the source package that can replace the
osdeps. For instance, the following code snippet, when added to overrides.rb,
will cause autoproj to look for external/opencv instead of opencv if the opencv
osdeps is not available on the current machine.</p>

<pre><code class="language-ruby">Autoproj.add_osdeps_overrides 'opencv', :package =&gt; 'external/opencv'
</code></pre>

<p>One can force the usage of the source package for the local installation
(completely ignoring the osdeps package)</p>

<pre><code class="language-ruby">Autoproj.add_osdeps_overrides 'opencv', :package =&gt; 'external/opencv', :force =&gt; true
</code></pre>



</div>
</div>
</div>
