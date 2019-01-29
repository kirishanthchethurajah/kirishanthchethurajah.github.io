---
layout: page
title: Moving to bundles
section: System Management
secondsection: Tutorials
---

<div class="content2">
<div class="content2-pagetitle">Moving to bundles</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="abstract">Abstract</h2>

<p>The following three tutorials will implement the same &ldquo;rock controlled by a joystick&rdquo; than
<a href="../tutorials/510_joystick.html">previous tutorials</a>, but using the model-based approach.</p>

<p>First of all, we&rsquo;ll introduce the concept of bundles.</p>

<p>In Rock, bundles offer a &ldquo;functional&rdquo; view to the available functionality, i.e.
instead of viewing only single components, bundles gather scripts,
configuration files and tools which are needed to run (sub)systems.</p>

<p>In practice, bundles are Rock packages and they - most importantly - contain
the following:</p>

<ul>
<li>ruby scripts (to run sets of components) - contained in the <em>scripts/</em> folder</li>
<li>configuration files for oroGen tasks - contained in the <em>config/orogen/</em> folder</li>
<li>models (including, but not limited to, for the system management layer)</li>
<li>data converter and data analysis scripts</li>
<li>datasets needed to run the system (as e.g. location information, maps, &hellip;)</li>
<li>&hellip;</li>
</ul>

<p>This list is incomplete, since bundles can contain arbitrary content and tools which support (sub)systems.</p>

<p>See <a href="http://rock.opendfki.de/wiki/WikiStart/Standards/RG7">this page</a> for more
information on bundles.</p>

<p>In this tutorial, we will create a new bundle &lsquo;tutorial&rsquo; and start developing in it.</p>

<p>It is assumed that your autoproj installation can be found in ~/dev.</p>

<h2 id="create-the-bundle">Create the bundle</h2>

<p>Create a new directory bundles/tutorial from the root of the rock
installation by going in bundles/ and doing</p>

<pre><code>cd ~/dev/bundles
rock-create-bundle tutorial
cd tutorial
syskit init
# ONLY FOR THIS TUTORIAL
# This normally not something you want to do
mv config/bundle.yml config/bundle-yml.bak
</code></pre>

<h2 id="logs-in-bundles">Logs in bundles</h2>
<p>When working with bundles, the component output, data logs and system management
logs are always saved in the logs/ folder, using a subfolder which matches the
time at which the process that generated the logs <em>started</em> (for example:
20111025-1105 for 25/10/2011 11:05). The directory containing the latest logs
(or the logs that are currently being generated) are symlinked from logs/current.</p>

<h2 id="move-on-to-the-next-tutorial">Move on to the next tutorial</h2>
<p>We&rsquo;re prepared, let&rsquo;s now dive into <a href="200_first_composition.html">building a model-based
application</a></p>



</div>
</div>
</div>
