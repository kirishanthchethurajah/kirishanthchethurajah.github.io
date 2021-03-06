---
layout: page
title: Advanced
section: Build System
secondsection: Supporting new OSes in autoproj
---
<div class="content2">
<div class="content2-pagetitle">Supporting new OSes in autoproj</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>This involves changing autoproj itself &hellip;</p>

<p>There is a bit of a chicken and egg problem, as you will need to have the
autoproj dependencies installed. In other words, <strong>you will need to install a
proper Ruby/RubyGems environment by yourself</strong>. Sorry &hellip; Once you&rsquo;ve got that,
run</p>

<p class="commandline">gem dependency autoproj</p>

<p>and install the Gems it lists.</p>

<h2 id="adding-support-in-autoproj">Adding support in autoproj</h2>

<ol>
 <li>
   <p>get the autoproj source code. It is <a href="http://github.com/rock-core/autoproj">on Github</a></p>

   <p>Also, add <em>autoproj_source_dir</em>/lib to RUBYLIB</p>
 </li>
 <li>
   <p>you will have to find out how your OS is detected by autoproj. To do so, run</p>

   <pre><code>autoproj --os-version
</code></pre>

   <p>On Debian, the result is:</p>

   <pre><code>name: debian
version:
 squeeze/sid
 unstable
 sid
</code></pre>

   <p>The interesting field is the &lsquo;name&rsquo; field. If the result of the above
command is &ldquo;no information about that OS&rdquo;, then you will need to modify the
operating_system method in <tt>lib/autoproj/osdeps.rb</tt> with custom
detection code.</p>
 </li>
 <li>
   <p>update the OS_AUTO_PACKAGE_INSTALL mapping in <tt>lib/autoproj/osdeps.rb</tt>.
Add a line of the form &lsquo;os_name&rsquo; =&gt; &lsquo;stanza needed to install a package&rsquo;
In the stanza, %s is replaced by a space-separated list of packages to
install. This placeholder <strong>must</strong> be quoted with single quotes (see other
OSes as examples). The command line must <em>not</em> expect any user intervention.</p>
 </li>
 <li>
   <p>update <tt>lib/autoproj/default.osdeps</tt> to define the autoproj
dependencies for your newly supported OS</p>
 </li>
</ol>

<h2 id="testing">Testing</h2>

<p>To test the new support, you will have to regenerate an autoproj gem and the
bootstrap script. In the autoproj source directory, run</p>

<pre class="commandline">
rake dist:package
rake dist:bootstrap
</pre>

<p>The package is generated in pkg/ and the bootstrap script in
bin/autoproj_bootstrap</p>

<p>You can now go into a fresh directory and run</p>

<pre class="commandline">
ruby _path_to_autoproj_/bin/autoproj_bootstrap
source env.sh
gem install _path_to_autoproj_/pkg/*.gem
autoproj bootstrap
</pre>

<p>If the last line went fine, then everything is probably OK. You can now try a
bigger install by bootstrapping an existing installation, as for instance</p>

<pre class="commandline">
autoproj bootstrap git git://github.com/rock-core/buildall.git
</pre>

<p><strong>And don&rsquo;t forget to <a href="../../contributing/index.html">send us your patches</a> !</strong></p>

<p class="content-txtbox-warning">Since you just added a new OS, none of the existing osdeps files will
have your OS in them. So you will need to edit the osdeps files from the package
sets and <a href="osdeps.html">add the relevant packages</a>.</p>



</div>
</div>
</div>
