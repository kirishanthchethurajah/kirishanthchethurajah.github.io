---
layout: page
title: Introduction
section: Build System
---

<div class="content2">
<div class="content2-pagetitle">Introduction</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="what-is-autoproj">What is Autoproj</h2>
<p>Autoproj allows to easily install and maintain software that is under source
code form (usually from a version control system). It has been designed to support a
package-oriented development process, where each package can have its own
version control repository (think &ldquo;distributed version control&rdquo;). It also
provides an easy integration of the local operating system (Debian, Ubuntu,
Fedora, maybe MacOSX at some point).</p>

<p>This tool has been developed in the frame of the RubyInMotion project
(http://sites.google.com/site/rubyinmotion), to install robotics-related
software &ndash; that is often bleeding edge. Unlike <a href="http://ros.org">the ROS build
system</a>, it is not bound to one build system, one VCS and one
integration framework. The philosophy behind autoproj
is:</p>

<ul>
<li>supports both CMake and autotools, and can be adapted to other tools</li>
<li>supports different VCS: cvs, svn, git, plain tarballs.</li>
<li>software packages are plain packages, meaning that they can be built and
installed /outside/ an autoproj tree, and are not tied <em>at all</em> to the
autoproj build system.</li>
<li>leverage the actual OS package management system. Right now, only Debian-like
systems (like Ubuntu) are supported, simply because it is the only one I have
access to.</li>
<li>handle code generation properly</li>
</ul>

<p>It tries as much as possible to follow the lead of Willow Garage on the package
specification. More specifically, the package manifest files are common between
ROS package management and autoproj (more details in the following of this
document).</p>

<h2 id="overview-of-an-autoproj-installation">Overview of an autoproj installation</h2>

<p>The idea in an autoproj installation is that people share <em>definitions</em> for a
set of packages that can depend on each other. Then, anyone can cherry-pick in
these definitions to build its own installation (in practice, one builds a
complete configuration per-project).</p>

<p>Each package definition includes:</p>

<ul>
<li>how to get the package&rsquo;s source code</li>
<li>how to build the package</li>
<li>on what the package depends. This can be either another package built by
autoproj, or an operating system package.</li>
</ul>

<p>See <a href="writing_manifest.html">this page</a> for more information.</p>

<h2 id="software-packages-in-autoproj">Software packages in Autoproj</h2>
<p>In the realm of autoproj, a software package should be a self-contained build
system, that could be built outside of an autoproj tree. In practice, it means
that the package writer should leverage its build system (for instance, CMake)
to discover if the package dependencies are installed, and what are the
appropriate build options that should be given (for instance, include
directories or library names).</p>

<p>As a guideline, we recommend that inter-package dependencies are managed by
using pkg-config.</p>

<p>To describe the package, and more importantly to setup cross-package
dependencies, <a href="advanced/manifest-xml.html">an optional manifest file can be
added</a>.</p>



</div>
</div>
</div>
