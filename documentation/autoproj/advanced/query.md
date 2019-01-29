---
layout: page
title: Advanced
section: Build System
secondsection: Tooling with shell scripts
---

<div class="content2">
<div class="content2-pagetitle">Tooling with shell scripts</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>Autoproj contains an &ldquo;autoproj query&rdquo; command that allows you to write shell
scripts to automate certain tasks. This page will explain the query structures,
and show some examples of what can be done with it.</p>

<p>autoproj query takes as argument a number of criteria as to what packages is of
interest to you, and allows you to output information about these packages.</p>

<p>The two sides of autoproj query are:</p>

<ul>
  <li>the query itself: how can you specify which packages to return</li>
  <li>the output formatting: what information can be extracted from autoproj and
displayed in the result</li>
</ul>

<h2 id="query-syntax">Query Syntax</h2>
<p>The general query syntax is</p>

<p class="block">FIELD1=VALUE1:FIELD2~VALUE2:FIELD3=VALUE3</p>

<p>The F=V notation means that the package&rsquo;s F must match V exactly. The F~V
notation means that F can be a partial match for V.</p>

<p>A field will match &ldquo;partially&rdquo; if:</p>

<ul>
  <li>the expected value can be found anywhere in the package field. The
match is done in a case-insensitive way. I.e. xsens would match both
drivers/xsens_imu and drivers/orogen/xsens_imu</li>
  <li>if the expected value contains &lsquo;/&rsquo; (directory marker), the package matches
the following regular expression: el\w+/el2\w+/el3\w+. I.e., d/o/xsens would
match drivers/orogen/xsens_imu</li>
</ul>

<p>The matches are combined with an AND, i.e. a package must match all of them to
be returned.</p>

<p>Finally, by default, <tt>autoproj query</tt> will only consider packages that
are selected in the manifest. If you mean to match all defined packages, use the
--search-all option.</p>

<h2 id="query-fields">Query Fields</h2>
<p>The following fields are available for query:</p>

<ul>
  <li>autobuild.name: the package name</li>
  <li>autobuild.srcdir: the package source directory</li>
  <li>autobuild.class.name: the package class</li>
  <li>vcs.type: the VCS type (as used in the source.yml files)</li>
  <li>vcs.url: the URL from the VCS. The exact semantic of it depends on the
VCS type</li>
  <li>package_set.name: the name of the package set that defines the package</li>
</ul>

<p>Some fields have shortcuts:</p>

<ul>
  <li>&lsquo;name&rsquo; can be used instead of &lsquo;autobuild.name&rsquo;</li>
  <li>&lsquo;class&rsquo; can be used instead of &lsquo;autobuild.class.name&rsquo;</li>
  <li>&lsquo;vcs&rsquo; can be used instead of &lsquo;vcs.url&rsquo;</li>
  <li>&lsquo;package_set&rsquo; can be used instead of &lsquo;package_set.name&rsquo;</li>
</ul>

<h2 id="output-formatting">Output Formatting</h2>
<p>By default, <tt>autoproj query</tt> will output the package names. This can be
customized with the --format option.</p>

<p>--format takes a string that can contain a number of $VAR fields. The following
fields are available for expansion:</p>

<ul>
  <li>NAME: the package name</li>
  <li>SRCDIR: the package source directory</li>
  <li>PREFIX: the package prefix</li>
  <li>SCORE: the match score for this package. See the API documentation of
Autoproj::Query#match for possible values.</li>
</ul>

<h2 id="examples">Examples</h2>

<p>Running &ldquo;git gc&rdquo; on every git package in the autoproj installation:</p>

<pre><code>commands=`autoproj query --format='git --git-dir=$SRCDIR/.git gc' vcs.type=git`
bash -c "$commands"
</code></pre>

<p>Tagging all packages from the imoby package set to a certain tag, and push it:</p>

<pre><code>for dir in `autoproj query '--format=$SRCDIR' vcs.type=git:package_set=imoby`; do
  git --git-dir=$dir/.git tag $NEWTAG
  git --git-dir=$dir/.git push
done
</code></pre>



</div>
</div>
</div>
