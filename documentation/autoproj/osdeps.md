---
layout: page
title: The osdeps system
section: Build System
---

<div class="content2">
<div class="content2-pagetitle">The osdeps system</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>Autoproj offers two ways to install software.</p>

<p>The primary intend of autoproj is to allow you to easily
install software that needs to be compiled. However, there are also ways to
install standard software packages that are offered by your operating system.</p>

<p>Autoproj&rsquo;s osdeps facility is there for that: handle dependencies between the
source packages that it needs to build and packages that are offered by the OS.
Example of such packages are: precompiled libraries and applications (e.g.
Debian packages on Debian and Ubuntu), RubyGem packages (rubygem is a package
management system for Ruby programs) and so on.</p>

<h2 id="declaring-and-inspecting-dependencies">Declaring and inspecting dependencies</h2>
<p>Dependencies on OS packages are declared the same way than cross-package
dependencies are. See <a href="advanced/osdeps.html">this page</a> for more
information.</p>

<p>It is important to note that, in case both an osdeps definition <em>and</em> an
autoproj package definition exist, the osdeps definition will take precedence &ndash;
to avoid unnecessarily compile software.</p>

<p>To inspect the OS dependencies on your autoproj installation, simply run</p>

<pre><code>autoproj osdeps --show
</code></pre>

<p>This will show the osdeps that are declared for each source package. The reverse
is also available with</p>

<pre><code>autoproj osdeps --rshow
</code></pre>

<p>which will show, for each needed OS package, which source package needs it.</p>

<h2 id="fine-tuning-the-osdeps-behaviour">Fine-tuning the osdeps behaviour</h2>
<p>When you bootstrap autoproj, a long question asks you to pick between four
osdeps mode. This page will give a bit more detail about each of the modes.</p>

<p><strong>In the &lsquo;all&rsquo; mode</strong>, autoproj will install both OS packages and RubyGem packages
when needed. You should not have to care about what the source packages need,
autoproj should install them. If the configuration or compilation of one of the
package fails, it will most probably be because the build configuration is
missing the relevant OS dependency.</p>

<p><strong>In the &lsquo;os&rsquo; mode</strong>, only OS packages will automatically be installed.
autoproj will <em>completely ignore</em> the RubyGem dependencies, so the build may
fail because some of them are not installed.</p>

<p><strong>In the &lsquo;ruby&rsquo; mode</strong>, only RubyGem packages will automatically be installed.
autoproj will <em>completely ignore</em> the OS package dependencies, so the build may
fail because some of them are not installed.</p>

<p><strong>In the &lsquo;none&rsquo; mode</strong>, neither type of packages will be handled by autoproj.
Indeed, the build may fail because some of them are not installed.</p>

<h2 id="behaviour-during-bootstrapping">Behaviour during bootstrapping</h2>
<p>Bootstrapping is a kind of a different beast. During bootstrapping, packages
<strong>must</strong> get installed as the goal is to get a functional autoproj.</p>

<p>Therefore, if you choose a different mode than &lsquo;all&rsquo;, autoproj will list
packages that need to be installed in order for the bootstrap to function
properly and wait for you to confirm that they are indeed installed.</p>

<h2 id="behaviour-on-operating-systems-os-not-supported-by-autoproj">Behaviour on operating systems (OS) not supported by autoproj</h2>
<p>In order to handle the OS packages, autoproj must know how to install them.
Operating systems for which autoproj does <em>not</em> have this information are marked
as &ldquo;unsupported&rdquo; (see <a href="advanced/autoproj_osdeps.html">this page</a> to extend autoproj for
your OS).</p>

<p>On these OS, the &lsquo;all&rsquo; and &lsquo;os&rsquo; options are obviously not available to you. You
will therefore have to install the packages by yourself (see below for a bit of
help from autoproj).</p>

<h2 id="getting-a-bit-of-help-from-autoproj-even-in-modes-different-than-all">Getting a bit of help from autoproj even in modes different than &lsquo;all&rsquo;</h2>

<p>First of all, as presented above, you can inspect osdeps definitions with
autoproj osdeps --show and autoproj osdeps --rshow</p>

<p>Another operation of interest is &ldquo;autoproj osdeps&rdquo; itself. It will install
missing the packages that you required (i.e. only RubyGem packages if you have
selected the &lsquo;ruby&rsquo; mode) <em>but</em> will also display the missing packages of the
other categories, allowing you to install them manually.</p>

<p>Moreover, you can ask autoproj to install packages that you have disabled with
the --all, --ruby and --os options:</p>

<pre><code>autoproj osdeps --all
</code></pre>

<p>will install all required packages, regardless of the &ldquo;global&rdquo; osdeps mode.</p>


</div>
</div>
</div>
