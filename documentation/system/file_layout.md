---
layout: page
title: Syskit-enabled Bundles
section: System Management
secondsection: system
---
<div class="content2">
<div class="content2-pagetitle">Syskit-enabled Bundles</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>When using Syskit, the bundles are first Roby applications. As such, the
description <a href="/api/tools/roby/building/file_layout.html">of the file layout in a Roby application</a> applies.</p>

<p>This page will start by explaining how one turns a plain Roby application into a
Syskit-enabled bundle. We will then summarize parts that are added in addition
to the Roby layout in a Roby application that uses Syskit.</p>

<h2 id="creating-syskit-enabled-roby-applications">Creating Syskit-enabled Roby applications</h2>

<p>The bundles created in Rock are already Syskit-enabled, i.e. if you do</p>

<pre><code>rock-create-bundle mybundle
cd mybundle
roby init
</code></pre>

<p>The resulting application is already using Syskit.</p>

<p>However, if you wrote a &ldquo;plain&rdquo; Roby application and want to use Syskit in it,
you should enable the Syskit plugin <em>and</em> pick the temporal scheduler. Edit
config/init.rb and add:</p>

<pre><code class="language-ruby">Roby.app.using 'syskit'
require 'roby/schedulers/temporal'
Roby.scheduler = Roby::Schedulers::Temporal.new
</code></pre>

<p>Once you have done that, run</p>

<pre><code>roby init
</code></pre>

<p>To add templates for Syskit-specific directories and files.</p>

<h2 id="syskit-specific-file-layout">Syskit-specific File Layout</h2>
<p>This section deals with the parts of the file layout that are specific to
Syskit. Refer to <a href="/api/tools/roby/building/file_layout.html">this page</a> for the
generic Roby parts</p>

<dl>
  <dt>config/orogen/</dt>
  <dd>YAML configuration files for the oroGen task contexts. The file names should
always be the name of the corresponding oroGen task context model (i.e.
xsens_imu::Task.yml for the xsens_imu::Task). See <a href="../runtime/configuration.html">this
page</a> for more details</dd>
  <dt>models/profiles/</dt>
  <dd><a href="profiles.html">profile definitions</a>. The file names should be the snake_case
version of the profile&rsquo;s name. For instance, an AvalonAUV profile should be
stored in models/profiles/avalon_auv.rb.</dd>
  <dt>models/blueprints/</dt>
  <dd>definitions of compositions and data services. One composition should be
defined in a single file, and the file names should be the snake_case
version of the composition name. For instance, a PoseEstimator composition should be
stored in models/blueprints/pose_estimator.rb. The data services should be
grouped &ldquo;by topic&rdquo; (e.g. all pose-related data services are stored in the rock
bundle under models/blueprints/pose). Finally, device models should always be
defined in a models/blueprints/devices.rb file, following the structure stored
in rock bundle&rsquo;s models/blueprints/devices.rb. The device file from other
bundles should be explicitly loaded there, prefixing the models/ folder by the
bundle name, as e.g.

    <pre><code class="language-ruby">require 'rock/models/blueprints/devices'
</code></pre>
  </dd>
  <dt>models/orogen/</dt>
  <dd>extensions to task context models. The file names must match the oroGen
project name of the extended tasks. These files are loaded on-demand, when the
corresponding oroGen project is loaded. See <a href="task_contexts.html">this page</a> for more details.</dd>
</dl>



</div>
</div>
</div>
