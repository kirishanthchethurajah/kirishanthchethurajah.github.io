---
layout: page
title: Bundles in Rock
section: Setting up a New Project
---

<div class="content2">
<div class="content2-pagetitle">Bundles in Rock</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>Bundles in Rock are collections of all files needed to run a particular robot
system.</p>

<p>In practice, bundles are Rock packages and they - most importantly - contain
the following:</p>

<ul>
 <li>ruby scripts (to run sets of components) - contained in the <em>scripts/</em> folder</li>
 <li>configuration files for oroGen tasks - contained in the <em>config/orogen/</em> folder</li>
 <li>log files - contained in the <em>logs/</em> folder</li>
 <li>&hellip;</li>
</ul>

<p>See <a href="http://rock.opendfki.de/wiki/WikiStart/Standards/RG7">the wiki</a> for more
information on bundles.</p>

<h2 id="creating-the-bundle">Creating the bundle</h2>

<p>The name of the bundle will be the basename of the directory created. Bundles
cannot created anywhere, use the <code>acd</code> command to change in the root-directory
of the Rock-Installation and then enter the <code>bundles</code> directory. Creating
bundles is possible in all directories named in the environment variable
<tt>$ROCK_BUNDLE_PATH</tt>.</p>

<pre><code>acd
cd bundles
rock-create-bundle my_new_bundle_name
</code></pre>

<p>The name of your bundle should, for logical reasons, equal the name of your
robot. Afterwards, you must tell Rock that this is the bundle you are currently
working on:</p>

<pre><code>rock-bundle-default my_new_bundle_name
</code></pre>


</div>
</div>
</div>
