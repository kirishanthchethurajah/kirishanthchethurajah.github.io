---
layout: page
title: Customizing the installation
section: Build System
---
<div class="content2">
<div class="content2-pagetitle">Customizing the installation</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="changing-the-installations-layout">Changing the installation&rsquo;s layout</h2>

<p>The <tt>layout</tt> section of <tt>autoproj/manifest</tt> offers two things:</p>

<ul>
 <li>select which packages/package sets to build and</li>
 <li>build packages in specific subdirectories</li>
</ul>

<p>This section lists packages or package sets that ought to be built by autoproj.
For instance, in the following example, only the <tt>orogen</tt> and
<tt>tools/orocos.rb</tt> packages of the rock.core package set and their
dependencies will be built. The other will be ignored.</p>

<pre><code class="language-yaml">package_sets:
 - github: rock-core/package_set

layout:
 - orogen
 - tools/orocos.rb
</code></pre>

<p>Instead of giving a package name, the name of a package set can be provided,
which translates into &ldquo;all the packages of that set&rdquo;.</p>

<p>As we mentionned before, the layout mechanism also allows you to place packages
in subdirectories of the main installation. For instance, the following snippet
will build the <tt>typelib</tt>, <tt>utilmm</tt> and <tt>utilrb</tt> libraries
of orocos-toolchain into the <tt>lib/</tt> subdirectory and all the <tt>orocos/</tt> packages in the root.</p>

<pre><code class="language-yaml">layout:
 - lib:
   - typelib
   - utilmm
   - utilrb
 - orocos/
</code></pre>

<p class="warning">Configuration files like <tt>autoproj/manifest</tt> are YAML file. As such, they
are sensible to indentation. The snippet above should be read as: in the layout,
there is first a &ldquo;lib&rdquo; part and second the packages whose names start with
&ldquo;orocos/&rdquo; <em>(first level of indentation)</em>. In the &ldquo;lib&rdquo; part, the packages are
&ldquo;typelib&rdquo;, &ldquo;utilmm&rdquo; and &ldquo;utilrb&rdquo; <em>(second level of indentation)</em>. </p>

<p>Alternatively, the example above could be written:</p>

<pre><code class="language-yaml">layout:
 - lib: [typelib, utilmm, utilrb]
 - orocos/
</code></pre>

<p>Finally, names of sublayouts can be used as arguments in the autoproj command
line, instead of package names:</p>

<p class="commandline">autoproj build lib</p>

<h2 id="exclude_packages">Removing packages from the build (<tt>exclude_packages</tt>)</h2>
<p>Instead of having to list all packages that you do want to build, it is possible
to list the packages that you don&rsquo;t want to build. Simply list them in the
<tt>exclude_packages</tt> section of the manifest file. In the following example, all
of the rock-core package set will be built <em>but</em> the pocolog package.</p>

<pre><code class="language-yaml">package_sets:
 - github: rock-core/package_set
layout:
 - rock.core
exclude_packages:
 - tools/pocolog
</code></pre>

<p>If another required package (i.e. a package listed in the layout) depends on an
excluded package, the default behaviour is to fail with
<a href="error_messages.html#exclusions">an error</a>.</p>

<p>For some package sets, such as the rock package set, this behaviour can be
relaxed by setting the package set&rsquo;s weak_dependencies flag to true in e.g. the
package set&rsquo;s init.rb or autoproj/init.rb:</p>

<p><code>ruby
metapackage('rock').weak_dependencies = true
</code></p>

<p>In this case, autoproj will only issue a warning and simply automatically
exclude the offending packages from the build.</p>

<h2 id="using-packages-that-are-already-installed-ttignorepackagestt">Using packages that are already installed (<tt>ignore_packages</tt>)</h2>

<p>If some packages are already installed elsewhere, and you want to use that
version instead of the one listed in the package sets, list them in the
<tt>ignore_packages</tt> section of the manifest. In the following example, the
<tt>rtt</tt> package will <em>not</em> be built by autoproj, but autoproj will assume
that it is present.</p>

<pre><code class="language-yaml">package_sets:
 - github: rock-core/package_set
layout:
 - rock.core
exclude_packages:
 - tools/pocolog
ignore_packages:
 - rtt
</code></pre>

<p>Unlike with <tt>exclude_packages</tt>, no error is generated for ignored
packages that are depended-upon by other packages in the build. This is because
ignore_packages is meant to list packages that are already installed outside of
autoproj, while exclude_packages lists what you do <strong>not</strong> want to have at all.</p>

<h2 id="local-overrides-of-version-control-information">Local overrides of version control information</h2>

<p>The <tt>autoproj/overrides.yml</tt> allows you to override version control information
for specific packages. It has the same format than the source.yml file of
package sets, so <a href="advanced/importers.html">check that page out</a> for more information.</p>

<p>This file can in particular be used to avoid further updates to a given software
package. Simply do:</p>

<pre><code class="language-yaml">overrides:
 - rtt:
   type: none
</code></pre>

<p>To track a different branch that the default branch, you will have to do:</p>

<pre><code class="language-yaml">overrides:
 - rtt:
   branch: experimental
</code></pre>

<h2 id="tuning-what-files-autoproj-looks-at-to-determine-if-a-package-should-be-updated">Tuning what files autoproj looks at to determine if a package should be updated</h2>
<p>When a package A depends on a package B, autoproj checks if some of the files in
B&rsquo;s directory are newer than the latest build of A. If it is the case, it will
trigger the build of B and then the one of A.</p>

<p>Autoproj has a default list of files that it should ignore. Unfortunately, it
may be that you are generating some files in the source directory that autoproj
interprets as new files and trigger builds.</p>

<p>If you have the impression that autoproj does too many rebuilds, run the build
once with the <tt>--list-newest-files</tt> option. For instance,</p>

<pre><code>autoproj --list-newest-files fast-build
</code></pre>

<p>If you find some files that should be ignored, add them either to the package
sets (i.e. autoproj/remotes/blablab/init.rb) or in autoproj/init.rb with</p>

<pre><code class="language-ruby">ignore /a_regular_expression/
</code></pre>

<p>where /a_regular_expression/ is a regular expression matching your files. For
instance, to eliminate all files that have an extension in &ldquo;.swp&rdquo;, one would do</p>

<pre><code class="language-ruby">ignore /\.swp$/
</code></pre>

<h2 id="building-local-packages">Building local packages</h2>

<p>You can list local packages that are not in an imported package set by placing
the definitions in autoproj/, in a file ending with <tt>.autobuild</tt>. See <a href="advanced/autobuild.html">this
page</a> for information on how to write autobuild files.</p>

<h2 id="setting-up-the-path-to-specific-commands-make-parallel-builds">Setting up the path to specific commands (make, parallel builds)</h2>

<p>The autobuild API allows to specify what specific installed command to use for
each tool needed during the build. These commands can be used in
<tt>autoproj/init.rb</tt> to tune the build system. Example:</p>

<pre><code class="language-ruby">Autobuild.programs['make'] = '/path/to/ccmake'
Autobuild.parallel_build_level = 10 # build with -j10
</code></pre>

<h2 id="more-complex-customization">More complex customization</h2>

<p>More complex customization can be achieved by accessing the <a href="/api/autoproj/index.html">Autoproj
API</a> and
the <a href="/api/autobuild/index.html">Autobuild API</a> directly in the <tt>autoproj/init.rb</tt> and
<tt>autoproj/overrides.rb</tt>
files. The former is loaded before all source files and the latter is loaded
after all source files.</p>

<p>Some examples:</p>

<ul>
 <li>
   <p>fixing dependencies: if a dependency is not listed in a package&rsquo;s manifest,
then you can fix it by adding the following in <tt>autoproj/overrides.rb</tt></p>

   <pre><code class="language-ruby">a = Autobuild::Package['a_package']
a.depends_on "other_package"
</code></pre>
 </li>
 <li>
   <p>changing the configuration of some cmake package:</p>

   <pre><code class="language-ruby">a = Autobuild::Package['a_package']
a.define "CONFIG_VAR", "CONFIG_VALUE"
</code></pre>
 </li>
</ul>

<h2 id="building-packages-selectively-on-the-command-line">Building packages selectively on the command line</h2>

<p>The autoproj command line accepts subdirectories defined in the layout as well
as package names.</p>

<p>For instance, with the following layout:</p>

<pre><code class="language-yaml">layout:
 - typelib
 - asguard:
   - modules/base
   - modules/logger
</code></pre>

<p>If the command line is</p>

<p class="commandline">autoproj build modules/logger</p>

<p>then modules/logger and its dependencies will be built. Idem, if the command line is:</p>

<p class="commandline">autoproj build asguard</p>

<p>then all packages or asguard/ are built, including their dependencies</p>



</div>
</div>
</div>
