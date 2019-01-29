---
layout: page
hero_text: about Page!
title: package_structure
section: Managing Packages
---

<div class="content2">
<div class="content2-pagetitle">Package Structure</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p class="note">Below, oroGen refers to the tool that is used in Rock to develop components</p>

<h2 id="categories">Categories</h2>
<p>The Rock packages are organized into categories:</p>

<ul>
<li><strong>base</strong> base types, CMake script repositories, &hellip;</li>
<li><strong>bundles</strong> <a href="http://trac.rock-robotics.org/wiki/WikiStart/Standards/RG7">bundle * packages</a></li>
<li><strong>control</strong> packages that are related to motion control</li>
<li><strong>data_processing</strong> packages that are related to general data processing (e.g.
neural network, filtering, &hellip;)</li>
<li><strong>drivers</strong> packages that are related to device drivers: drivers themselves,
and common libraries that ease their development</li>
<li><strong>gui</strong> GUI and visualization packages</li>
<li><strong>image_processing</strong> packages that are related to image processing</li>
<li><strong>multiagent</strong> packages that are related to multiagent / multirobot
coordination</li>
<li><strong>planning</strong> packages that are related to path and task planning</li>
<li><strong>simulation</strong> packages that are related to simulation</li>
<li><strong>slam</strong> packages that are related to localization and mapping both separately
and as SLAM</li>
<li><strong>test</strong> packages that are needed for other unit tests</li>
<li><strong>tools</strong> packages that are related to the toolchain and/or are general
utility packages</li>
<li><strong>tutorials</strong> packages that are the result of tutorials</li>
</ul>

<p>On github, each category has its own project, called rock-NAME (for instance,
rock-drivers for the drivers). However, Rock is still in the process of
migrating packages from gitorious to github, so some of the existing packages
are still on gitorious. <strong>All new packages should go to github</strong>.</p>

<p>When installed, packages go into folders that correspond to their main category.
Moreover, the oroGen-independent packages are installed directly under that
folder, while the oroGen components are installed in an &lsquo;orogen&rsquo; subfolder.</p>

<p>For instance, the driver libraries are stored in drivers/ and the driver oroGen
components in drivers/orogen/</p>

<ul>
<li>no other subdirectories other than &ldquo;orogen&rdquo; can be created under the main
categories</li>
<li>opening new categories is indeed possible but <strong>must</strong> be discussed first on
the mailing list.</li>
</ul>

<h2 id="naming">Naming</h2>

<ul>
<li>snake_case for all path components (categories and package names)</li>
</ul>

<h2 id="libraries-and-orogen-components">Libraries and oroGen components</h2>
<p>The most important design factor in the Rock package structure is that
functionality should be implemented in a way that is <strong>independent from any
integration framework</strong></p>

<p>In practice, it means that for most functionality, there will be two Rock
packages:</p>

<ul>
<li>the &ldquo;library&rdquo; part which usually is a C++ library, that uses CMake to build,
with maybe some dependencies on other C++ libraries (other Rock libraries
and/or &ldquo;common&rdquo; libraries)</li>
<li>the &ldquo;orogen&rdquo; part which is providing an integrated oroGen component for the
libraries.</li>
</ul>

<p>For instance, in the <a href="https://github.com/rock-drivers">rock-drivers</a>
subproject, there is <a href="https://github.com/rock-drivers/drivers-hokuyo">the Hokuyo driver
library</a> and the corresponding <a href="https://github.com/rock-drivers/drivers-orogen-hokuyo">oroGen
component</a>.</p>

<h2 id="mapping-between-local-installation-and-github-repositories">Mapping between local installation and github repositories</h2>

<p>A library (non-oroGen package) installed on the local system as</p>

<pre><code>category/package_name
</code></pre>

<p>will be managed in a GitHub repository called</p>

<pre><code>http://github.com/GITHUB_ORGANIZATION/category-package_name
</code></pre>

<p>Where GITHUB_ORGANIZATION is usually but not necessarily rock-category.</p>

<p>An oroGen package installed on the local system as</p>

<pre><code>category/orogen/package_name
</code></pre>

<p>will be managed in a GitHub repository called</p>

<pre><code>http://github.com/GITHUB_ORGANIZATION/category-orogen-package_name
</code></pre>

<p>When a one-to-one mapping exists between a library and an oroGen package (e.g.
the hokuyo driver library and the hokuyo oroGen component), both will have the
same &ldquo;package_name&rdquo;. For instance, when installed</p>

<pre><code>drivers/hokuyo
drivers/orogen/hokuyo
</code></pre>

<p>and on GitHub:</p>

<pre><code>http://github.com/rock-drivers/drivers-iodrivers_base
http://github.com/rock-drivers/drivers-orogen-iodrivers_base
</code></pre>



</div>
</div>
</div>
