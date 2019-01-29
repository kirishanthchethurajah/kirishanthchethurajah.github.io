---
layout: page
title: Advanced
section: Build System
secondsection: Writing autobuild scripts
---
<div class="content2">
<div class="content2-pagetitle">Writing autobuild scripts</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="defining-cmake-packages">Defining CMake packages</h2>
<p>A simple CMake package is defined with</p>

<pre><code class="language-ruby">cmake_package "package_name"
</code></pre>

<p>More complex tweaking is achieved with</p>

<pre><code class="language-ruby">cmake_package "package_name" do |pkg|
 [modify the pkg object]
end
</code></pre>

<p>In particular, CMake build options can be given with</p>

<pre><code class="language-ruby">cmake_package "package_name" do |pkg|
 pkg.define "VAR", "VALUE"
end
</code></pre>

<p>The above snippet being equivalent to calling <tt>cmake -DVAR=VALUE</tt></p>

<p class="block">The &ldquo;pkg&rdquo; variable in the example above is an instance of <a href="../../../api/autobuild/Autobuild/CMake.html">Autobuild::CMake</a></p>

<h2 id="autotools">Defining autotools packages</h2>

<pre><code class="language-ruby">autotools_package "package_name"
autotools_package "package_name" do |pkg|
   pkg.configureflags &lt;&lt; "--enable-feature" &lt;&lt; "VAR=VALUE"
   # additional configuration
end
</code></pre>

<p class="block">The &lsquo;pkg&rsquo; variable in the example above is an instance of
<a href="../../../api/autobuild/Autobuild/Autotools.html">Autobuild::Autotools</a></p>

<p>Since autotools (and specifically, automake) environments are unfortunately
not so reusable, autoproj tries to regenerate the autotools scripts forcefully.
This can be disabled by setting the some flags on the package:</p>

<ul>
 <li>using[:aclocal]: set to false if aclocal should not be run</li>
 <li>using[:autoconf]: set to false if the configure script should not be touched</li>
 <li>using[:automake]: set to false if the automake part of the configuration
should not be touched</li>
 <li>using[:libtool]: set to false if the libtool part of the configuration should
not be touched</li>
</ul>

<p>For instance, one would add</p>

<pre><code class="language-ruby">autotools_package "package_name" do |pkg|
   pkg.configureflags &lt;&lt; "--enable-feature" &lt;&lt; "VAR=VALUE"
   # Do regenerate the autoconf part, but no the automake part
   pkg.using[:automake] = false
end
</code></pre>

<h2 id="defining-ruby-packages">Defining Ruby packages</h2>

<pre><code class="language-ruby">ruby_package "package_name"
ruby_package "package_name" do |pkg|
   # additional configuration
end
</code></pre>

<p>This package handles pure ruby libraries that do not need to be installed at
all. Autoproj assumes that the directory layout of the package follows the following
convention:</p>

<ul>
 <li>programs are in bin/</li>
 <li>the library itself is in lib/</li>
</ul>

<p>If a Rakefile is present in the root of the source package, its <tt>default</tt>
task will be called during the build, and its <tt>redocs</tt> task will be used
for documentation generation. The <tt>rake_setup_task</tt> and
<tt>rake_doc_task</tt> package properties can be used to override this default
setting:</p>

<pre><code class="language-ruby">ruby_package "package_name" do |pkg|
   pkg.rake_setup_task = "setup"
   pkg.rake_doc_task = "doc:all"
end
</code></pre>

<p>Additionally, they can be set to <tt>nil</tt> to disable either setup or documentation
generation. For instance, the following code disables documentation generation
and uses the +setup+ task at build time:</p>

<pre><code class="language-ruby">ruby_package "package_name" do |pkg|
   pkg.rake_setup_task = "setup"
   pkg.rake_doc_task = nil
end
</code></pre>

<p class="block">The &lsquo;pkg&rsquo; variable in the example above is an instance of
<a href="../../../api/autobuild/Autobuild/ImporterPackage.html">Autobuild::ImporterPackage</a>
with additional methods coming from
<a href="../../../api/autoproj/RubyPackage.html">RubyPackage</a></p>

<h2 id="defining-orogen-packages">Defining oroGen packages</h2>

<pre><code class="language-ruby">orogen_package "package_name"
orogen_package "package_name" do |pkg|
   # customization code
end
</code></pre>

<p>oroGen is a module generator for the Orocos component framework. See <a href="../../orogen">the oroGen
documentation</a> for more information.</p>

<p class="block">The &lsquo;pkg&rsquo; variable in the example above is an instance of
<a href="../../../api/autobuild/Autobuild/Orogen.html">Autobuild::Orogen</a></p>

<h2 id="not_on_and_only_on">OS-specific bits (<tt>not_on</tt> and <tt>only_on</tt>)</h2>
<p>It is possible to have some parts of the autobuild file be OS-specific. Two
calls are made available for that.</p>

<p>First, if you know that some packages should not be built on some operating
systems, you should enclose their declaration in a &lsquo;not_on&rsquo; statement. For
instance:</p>

<pre><code class="language-ruby">not_on 'debian' do
 cmake_package 'excluded_package'
end
</code></pre>

<p>It is additionally possible to select specific versions</p>

<pre><code class="language-ruby">not_on 'debian', ['ubuntu', '10.04'] do
 cmake_package 'excluded_package'
end
</code></pre>

<p>If, on the other hand, you want some bits to be available only <strong>on</strong> a specific
OS, use the only_on statement:</p>

<pre><code class="language-ruby">only_on ['ubuntu', '10.04'] do
 cmake_package 'only_ubuntu'
end
</code></pre>

<p>If the user tries to build a package that is excluded on his architecture, he
will get the following error message:</p>

<p class="cmdline">modules/dynamixel depends on drivers/dynamixel, which is excluded from the build: drivers/dynamixel is disabled on this operating system</p>

<h2 id="custom-package-building">Custom package building</h2>

<pre><code class="language-ruby">import_package "package_name" do |pkg|
   pkg.post_install do
       # add commands to build and install the package
   end
end
</code></pre>

<p>See <a href="writing_package_handlers.html">this page</a> about some <strong>very important</strong> issues when writing such command
blocks.</p>

<h2 id="declaring-documentation-targets">Declaring documentation targets</h2>
<p>Both autotools and CMake packages use <tt>make</tt> as the low-level build tool.
For both packages, you can declare a documentation target that will be used
during the call to <tt>autoproj doc</tt> to generate documentation:</p>

<pre><code class="language-ruby">cmake_package "package_name" do |pkg|
 pkg.with_doc 'doc'
 pkg.doc_dir = "doc/html"
end
</code></pre>

<p>The <tt>doc_dir</tt> assignment above is needed if the package installs its documentation
elsewhere than &ldquo;doc&rdquo;.</p>

<h2 id="defining-dependencies">Defining dependencies</h2>
<p>Inter-package dependencies can be defined with</p>

<pre><code class="language-ruby">pkg.depends_on "package_name"
</code></pre>

<p>Where package name is either the name of another autoproj-built package, or the
name of a package that is to be <a href="osdeps.html">provided by the operating system</a>.</p>

<p class="warning">Both methods should be used only for dynamic dependencies, i.e. dependencies
that are dependent on build options (see below). Static dependencies should be
defined in <a href="manifest-xml.html">the package&rsquo;s manifest.xml</a></p>

<p>Finally, it is possible to give aliases to a package&rsquo;s name, by using the
Autobuild::Package#provides method. If one does</p>

<pre><code class="language-ruby">cmake_package "mypkg" do |pkg|
   pkg.provides "pkgconfig/libmypkg"
end
</code></pre>

<p>then a package that declares a dependency on &ldquo;pkgconfig/libmypkg&rdquo; will actually
depend on &ldquo;mypkg&rdquo;.</p>

<h2 id="defining-and-using-options">Defining and using options</h2>

<p>It is possible to define configuration options which are set by your user at
build time. These options can then be used in the autobuild scripts to
parametrize the build.</p>

<p>The general form of an option declaration is:</p>

<pre><code class="language-ruby">configuration_option "option_name", "option_type",
   :default =&gt; "default_value",
   :values =&gt; ["set", "of", "possible", "values"],
   :doc =&gt; "description of the option"
</code></pre>

<p>Once declared, it can be used in autobuild scripts with:</p>

<pre><code class="language-ruby">user_config("option_name")
</code></pre>

<p>Options are saved in <tt>autoproj/config.yml</tt> after the build. Options that
are already set won&rsquo;t be asked again unless the <tt>--reconfigure</tt> option is
given to <tt>autoproj build</tt>.</p>

<p class="warning">Do not try to have too many options, that is in general bad policy as
non-advanced users won&rsquo;t be able to know what to answer. Advanced users will
always have the option to override your autobuild definitions to tweak the
builds to their needs.</p>



</div>
</div>
</div>
