---
layout: page
title: Advanced
section: Build System
secondsection: Creating a package set
---
<div class="content2">
<div class="content2-pagetitle">Creating a package set</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>A package set is made of three things:</p>

<ul>
<li>the description file (source.yml)</li>
<li>an optional initialization script (init.rb) and override script
(overrides.rb)</li>
<li>autobuild scripts (*.autobuild)</li>
</ul>

<p>To create a new package set that is going to be shared with others, the simplest
solution is to</p>

<ul>
<li>create a new directory</li>
<li>add a source.yml file with just the set name in it:</li>
</ul>

<pre><code class="language-yaml">name: my_package_set_name
</code></pre>

<ul>
<li>
  <p>push it to whatever VCS you want. For git and github, you would do
something like</p>

  <pre><code>git init
git add source.yml
git commit -m "create the my_package_set_name package set"
git push git@github.com:my-project/package_set.git
</code></pre>
</li>
<li>
  <p>add the package set to autoproj/manifest and run</p>

  <pre><code>autoproj update-config
</code></pre>
</li>
</ul>

<p>At this stage, the empty set is properly checked out in
autoproj/remotes/my_package_set_name. You can add package definition and VCS
information (see below), test it with autoproj update an autoproj build, commit
and push !</p>

<p>If you are <strong>not</strong> going to share the package set, the simplest solution is to
just add the autobuild and version control information in
autoproj/local.autobuild and autoproj/overrides.yml &ndash; i.e. to not create a
separate package set at all.</p>

<h2 id="adding-a-package">Adding a package</h2>

<p>Adding a package to a package set involves changing two files:</p>

<ul>
<li>one of the package set&rsquo;s autobuild file that declares what packages there
are. Any file ending with .autobuild is loaded as an autobuild file by
autoproj.</li>
<li>the package set&rsquo;s source.yml file that declares where to get it (version
control information)</li>
</ul>

<p>For the first step, you need to add one of the following lines:</p>

<pre><code class="language-ruby">cmake_package "my/package" # for CMake package
autotools_package "my/package" # for autoconf/automake packages
orogen_package "my/package" # for orogen packages
import_package "my/package" # for custom packages
ruby_package "my/package" # for ruby libraries (optionally with C/C++ extensions)
</code></pre>

<p>The package name will be used to refer to this particular package later on &ndash;
especially for version control definition. If subdirectories are used, like &ldquo;my&rdquo;
in the above example, the package source will be checked out and built in the
corresponding subdirectory. For instance, with</p>

<pre><code class="language-ruby">cmake_package "drivers/hokuyo"
</code></pre>

<p>the hokuyo driver will <em>always</em> be built in a <tt>drivers/</tt> subdirectory.</p>

<p>Now that the package is declared, we need to add version control information to
the source.yml file. This needs to be done in the version_control section of
the file, as for instance:</p>

<pre><code class="language-yaml">version_control:
- my/package:
    type: git
    url: git://github.com/blabla/my-package.git
</code></pre>

<p>The corresponding subversion definition would be:</p>

<pre><code class="language-yaml">version_control:
- my/package:
    type: svn
    url: svn+ssh://svnhosting.com/blabla/trunk/my/package
</code></pre>

<p>For testing purposes, it is possible to tell autoproj to <em>not</em> take into account
any VCS information:</p>

<pre><code class="language-yaml">version_control:
- my/package: none
</code></pre>

<p>See <a href="importers.html">this page</a> for details on the import mechanisms.</p>

<h2 id="autobuild-scripts">Autobuild scripts</h2>
<p>The autobuild scripts lists all the packages defined by this set. It is a
Ruby script (but you don&rsquo;t need to know Ruby to write one). In its most simple
form, it looks like:</p>

<pre><code class="language-ruby">cmake_package "orocos/rtt"
autotools_package "drivers/imu"
orogen_package "modules/imu"
import_package "external/sisl"
ruby_package "orocos/orocos.rb"
</code></pre>

<p>The above snippet lists the kind of packages autoproj currently supports. The first one uses CMake as a build
system and will be installed in the <tt>orocos/rtt</tt> subdirectory of the
autoproj installation. The second one is an autotools package. Additionally, there is support for
orogen packages (i.e. Orocos components generated with orogen), packages that should be just imported but not build, and for Ruby packages. Package definitions can be tweaked quite a lot, including the ability to generate
documentation. See <a href="autobuild.html">the next page</a> for more information on how to write autobuild
scripts.</p>

<h2 id="sourceyml">source.yml</h2>
<p>The source.yml file gives generic information on the package set itself (most
importantly its name), and version control information (i.e. where to get the
packages). It is a YAML file, and looks like:</p>

<pre><code class="language-yaml">name: rock.drivers
constants:
ROOT_DIR: $HOME/share

version_control:
- "modules/.*":
    type: git
    url: $ROOT_DIR/$PACKAGE_BASENAME.git
- "drivers/.*":
    type: svn
    url: svn+ssh://rlbsvn.informatik.uni-bremen.de/trunk/$PACKAGE.git
</code></pre>

<p>The name field gives a name for the set. It is arbitrary, but the guideline
is to give a name that is java-like for namespaces, i.e. origin.name.</p>

<p>The <tt>constants:</tt> section lists values that can be reused for different
packages. Autoproj defines muliple constants:</p>

<ul>
<li>HOME is the user&rsquo;s home directory,</li>
<li>PACKAGE is the expansion of the (complete) regular expression given as package name, i.e. here it might be
&lsquo;modules/yourpackage&rsquo;</li>
<li>PACKAGE_BASENAME is the actual package name, useful when using wildcards in package
names (see below)</li>
<li>AUTOPROJ_SOURCE_DIR path to the directory of the file which contains this constant</li>
<li>AUTOPROJ_ROOT path to the main installation folder</li>
<li>AUTOPROJ_CONFIG path to the autoproj configuration directory</li>
</ul>

<p>It is also possible to use configuration variables, that get asked to the user
during the build (see below).</p>

<p>Finally, the <tt>version_control:</tt> section describes how to import each
software package. Its general format is:</p>

<pre><code class="language-yaml">package_name:
type: version_control_type # git, svn, cvs, darcs
url: repository_url
</code></pre>

<p>Where package_name is a regular expression that matches the package name (for
instance, &ldquo;.*&rdquo; will match all packages and &ldquo;drivers/.*&rdquo; will match packages
whose name starts with &lsquo;drivers&rsquo;). The package name is the one given to the
<tt>blabla_package</tt> stanza in the autobuild file.</p>

<p>For the git importer, one of &lsquo;branch&rsquo; or &lsquo;tag&rsquo; options can be provided as well:</p>

<pre><code class="language-yaml">package_name:
branch: branch_to_track
tag: tag_to_stick_to # it is branch OR tag
</code></pre>

<p>The options are applied in order, meaning that the top entries will be overridden
by the lower ones. In general, one will have a &ldquo;.*&rdquo; entry to give options for
all packages, and then override for specific packages:</p>

<pre><code class="language-yaml">version_control:
- .*: # common options for all packages
    type: git
    url: $ROOT_DIR/$PACKAGE.git

- "modules/logger": # we don't follow master on this module
    branch: imoby
</code></pre>

<h2 id="interaction-between-package-sets-definition-files">Interaction between package sets definition files</h2>
<p>When multiple package sets are used, it is possible to override the version
control definitions in low-priority sets with a higher priority one. Autoproj
has the following rules when searching for version control information:</p>

<ul>
<li>autoproj looks at the package sets <em>in the order they appear in the
installation manifest</em></li>
<li>autoproj searches a relevant version_control field, and stops at the first
package set that has one.</li>
<li>autoproj <em>stops searching</em> at the package set that defines the package.
Consequence: this set <em>must</em> have a version_control field for it, and an
error will be issued otherwise.</li>
</ul>

<h2 id="configopts">Using configuration options</h2>
<p>autoproj offers a configuration system that allows the user to tweak the build
to its needs. If the version control definitions depend on such configuration
options (as, for instance, because you want to parametrize the importer URLs),
then create an init.rb file next to the source.yml file, and add lines looking
like:</p>

<pre><code class="language-ruby">configuration_option "option_name", "option_type",
  :default =&gt; "default_value",
  :values =&gt; ["set", "of", "possible", "values"],
  :doc =&gt; "description of the option"
</code></pre>

<p>where the option_type field can either be &ldquo;string&rdquo; or &ldquo;boolean&rdquo;.</p>

<p>Then, you can use the option_name as an expansion in the source.yml file.</p>

<p>For instance, at my lab we are using an share filesystem to store the git
repositories. Our project&rsquo;s init.rb file has the following option definition:</p>

<pre><code class="language-ruby">configuration_option "MOUNT_POINT", "string",
  :default =&gt; "$HOME/nfs",
  :doc =&gt; "mount point of the NFS server"
</code></pre>

<p>And the source.yml uses it with:</p>

<pre><code class="language-yaml">version_control:
".*":
  url: $MOUNT_POINT/git/$PACKAGE.git
  type: git
</code></pre>



</div>
</div>
</div>
