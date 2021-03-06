---
layout: page
title: Understanding autoproj error messages
section: Build System
---

<div class="content2">
<div class="content2-pagetitle">Understanding autoproj error messages</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="i-know-nothing-about-a-prepackaged-package-called-xxxx-">I know nothing about a prepackaged package called &lsquo;XXXX&rsquo;, &hellip;</h2>
<p><em>Short explanation</em>: an OS dependency is listed by one of the packages, but no
definition exists in one of the osdeps files.</p>

<p><em>Long explanation</em>: XXXX is listed as an operating system dependency by one of
the packages that you requested to build. Two solutions: (i) the package should
<em>not</em> depend on it, and you should modify the <a href="advanced/manifest-xml.html">package&rsquo;s
manifest.xml</a> file. (ii)
the package should depend on it and you should list the OS package in <a href="advanced/osdeps.html">the
relevant osdeps file</a></p>

<h2 id="exclusions">XXX depends on YYY, which is excluded from the build</h2>

<p>The layout requires the XXX package to be built, but this package depends on YYY
and YYY has explicitly been excluded from the build.</p>

<p>There are two cases.</p>

<p>In the first case, the package has been listed in <a href="customization.html#exclude_packages">the exclude_packages section
of autoproj/manifest</a>. So, you will have to
find out why and either remove it from there, or add XXX to the same section.</p>

<p>In the second case, the package is disabled on your operating system version.
This is done in the package set&rsquo;s autobuild files by using the <a href="advanced/autobuild.html#not_on_and_only_on">not_on and
only_on statements</a>. You will
have to either exclude the XXX package as well (either by including it in the
same not_on/only_on block, or by adding it to <a href="customization.html#exclude_packages">the exclude_packages section
of autoproj/manifest</a>), or to make it so
that YYY builds on your OS, an thus remove it from the not_on/only_on block.</p>



</div>
</div>
</div>
