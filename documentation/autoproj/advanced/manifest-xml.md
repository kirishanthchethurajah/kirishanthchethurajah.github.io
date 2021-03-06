---
layout: page
title: Advanced
section: Build System
secondsection: The manifest.xml file
---

<div class="content2">
<div class="content2-pagetitle">The manifest.xml file</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>The manifest.xml is an optional (but <em>very recommended</em>) file that lies in the
root of every source package. This file describes both what the package <em>is</em>
(i.e. short description, contact information), and dependency information (what
should be installed for that package to be built successfully).</p>

<p>The general form of the file is:</p>

<pre><code class="language-xml">&lt;package&gt;
 &lt;description brief="one line of text"&gt;
   long description goes here,
   &lt;em&gt;XHTML is allowed&lt;/em&gt;
 &lt;/description&gt;
 &lt;author&gt;Alice/alice@somewhere.bar, Bob/bob@nowhere.foo&lt;/author&gt;
 &lt;license&gt;BSD&lt;/license&gt;
 &lt;url&gt;http://sites.google.com/site/rubyinmotion&lt;/url&gt;
 &lt;logo&gt;http://sites.google.com/site/rubyinmotion&lt;/logo&gt;

 &lt;!-- add dependency to another autoproj-built
 package --&gt;
 &lt;depend package="pkgname"/&gt;
 &lt;depend package="common"/&gt;


  &lt;!-- add dependency to an OS-provided package --&gt;
  &lt;rosdep name="os-pkgname" /&gt;
&lt;/package&gt;
</code></pre>



</div>
</div>
</div>
