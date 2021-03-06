---
layout: page
title: Packages
section : Contributing
---
<div class="content2">
<div class="content2-pagetitle">Packages</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>There are two options for you to contribute packages to Rock:</p>

<ul>
<li>make your package a part of Rock &lsquo;proper&rsquo;, i.e., of the Rock package set</li>
<li>create <a href="../autoproj/advanced/creating_pkg_set.html">your own package set</a></li>
</ul>

<p>This page will describe the process for both options.</p>

<h2 id="add-your-package-to-rock">Add your package to Rock</h2>

<p>As a side note, it is important that you realize that your library can be
integrated into Rock <em>without any changes</em> to your library. The Rock build system is designed for
that, as long as it follows widely-accepted standards (see for instance
the &ldquo;Build System Behaviour&rdquo; section of the <a href="http://rock.opendfki.de/wiki/WikiStart/Standards/RG4">corresponding Rock
guidelines</a>). Even the
<a href="../autoproj/advanced/manifest-xml.html">manifest.xml file</a>, which is used to
describe the package, can be provided as part of the package set instead
of as part of the package itself.</p>

<p>The process to get a package into Rock is:</p>

<ul>
<li>locally modify the Rock package set (in autoproj/remotes/rock) to define your
package. The package should go in the libs.autobuild file if it is a library
and in the orogen.autobuild file if it is an oroGen package.</li>
<li>commit these changes</li>
<li><a href="merge_changes_to_rock-core.html">send these changes to the Rock developers</a>. Make sure that
the purpose of the package(s) is clear.</li>
<li>at this point, there is the option of putting the source code within Rock&rsquo;s
github projects. While it is not mandatory, it is the preferred option.
The Rock developers will be in touch with you to discuss this (either through
the <a href="http://www.dfki.de/mailman/cgi-bin/listinfo/rock-users">rock-users mailing list</a> or through the merge request / ticket you created)</li>
</ul>

<h2 id="publish-your-package-set-in-rock">Publish your package set in Rock</h2>

<p>If you have integrated more than one package, and want to keep control over
these packages (i.e., you want them to appear to be a separate contribution from your
company / research institute), there is the possibility to submit a package set.
This package set will be added to Rock&rsquo;s default build configuration, and will
be integrated into the Rock package directory.</p>

<p>The process to submit a package set is:</p>

<ul>
<li>send the link and description of the package set to the <a href="http://www.dfki.de/mailman/cgi-bin/listinfo/rock-users">rock-users mailing
list</a></li>
<li>wait for a Rock developer to pick up on that</li>
</ul>



</div>
</div>
</div>
