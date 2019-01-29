---
layout: page
title: Introduction
section: Advanced
---

<div class="content2">
<div class="content2-pagetitle">Introduction</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="advanced-rock-tutorials">Advanced Rock Tutorials</h2>
<p>After having successfully gone through the <a href="../tutorials/index.html">basic
tutorials</a>, you might be interested in
going deeper into Rock.  Tutorials in this section deal with more advanced
topics, giving you more freedom to setup your system.</p>

<h2 id="what-to-expect-from-the-advanced-tutorials">What to expect from the advanced tutorials</h2>
<p>These tutorials are less structured than the basic tutorials. They cover
specific topics that you might want to learn about:</p>

<ul>
<li><strong>Deployments</strong>. The basic tutorials showed you how to run tasks. This
tutorial will show you how to customize how tasks should be executed for your
particular system. For instance, one can declare that a task is periodic with
a period of 0.1s by default, but for a particular system, choose a period of
1s.</li>
<li><strong>Log data and replay</strong>. These tutorials will describe how to turn on
logging, manage the resulting logfiles and replay the data in running
components for e.g. testing.</li>
<li><strong>Vizkit Widgets and Vizkit 3D plugin creation</strong>. How to extend Vizkit,
Rock&rsquo;s visualization library, by adding new widgets and/or new 3D
visualization plugins. How to use the Qt designer to create graphical
interfaces.</li>
<li><strong>Using avahi</strong>. How to use avahi (a common
<a href="http://en.wikipedia.org/wiki/Multicast_DNS">mDNS</a> implementation) to resolve
the task names instead of a centralized CORBA name server.</li>
</ul>

<h2 id="installing">Installing the tutorial results</h2>
<p>In case you don&rsquo;t want to write all the packages yourself, there is a package
available for the Rock tutorials so that you can easily retrieve them.
If you want to add the tutorials to your installation, add the package set to
your autoproj/manifest.</p>

<p>autoproj/manifest should look like:</p>

<pre><code class="language-text">package_sets:
- github: rock-core/package_set
- github: rock-tutorials/package_set

layout:
- rock.core
- rock.tutorials
</code></pre>



</div>
</div>
</div>
