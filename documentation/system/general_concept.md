---
layout: page
title: General Concept
section: System Management
secondsection: system
---
<div class="content2">
<div class="content2-pagetitle">General Concept</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>The general concept in Syskit, the rock system management layer, is to provide
requirements on what should run, and let the management layer (1) generate the
network that is needed to run it and (2) let it handle the runtime aspects
(configuration / reconfiguration, starting, monitoring)</p>

<p class="warning">It would be a <strong>very</strong> good idea to follow <a href="../system_management_tutorials/index.html">the
tutorials</a> before you continue with
this documentation.</p>

<h2 id="bundles">Bundles</h2>
<p>Bundles in Rock are used to gather all that form a consistent robot application
together. Outside of Syskit, they gather configuration files (<a href="../runtime/configuration.html">for task
contexts</a> and for outside tooling such as the
<a href="../data_processing/">transformer</a></p>

<p>When using Syskit, they also will contain all the Syskit-specific models and
application information. Whenever you start a new Syskit-based application,
create a new bundle and convert it to a Syskit/Roby application with:</p>

<pre><code class="language-ruby">rock-create-bundle desired/path/to/my/bundle
cd desired/path/to/my/bundle
roby init
</code></pre>

<p>Converting an existing bundle is simply done by running &ldquo;roby init&rdquo; in it.</p>

<p>Bundles can depend on each other, allowing to reuse models from one to the next.
This is done by editing config/bundle.yml and adding a line in the
bundle/dependencies section:</p>

<pre><code class="language-yaml">bundles:
   dependencies:
       - rock
       - rock_ugv_nav
</code></pre>

<p>All newly created bundles depend on the Rock bundle by default.</p>

<p class="warning">When adding a bundle dependency, don&rsquo;t forget to add the corresponding package
in the bundle&rsquo;s manifest.xml file. Bundles are usually stored in the bundles/
folder, so a bundle called &ldquo;X&rdquo; maps to a package called &ldquo;bundles/X&rdquo;</p>

<p>Whenever it applies, the documentation will state where the bundle directory
structure each parts of a Syskit application should be placed. Then, this is
going to be summarized <a href="file_layout.html">at the end of this guide</a>.</p>

<h2 id="components">Components</h2>
<p>In Syskit, a <em>component</em> designates a black box
that has inputs and outputs, and can report about its execution. In practice, it
can be:</p>

<ul>
 <li>a <em>task context</em>, i.e. an actual component that is <a href="../orogen/">defined in oroGen and
implemented in C++</a></li>
 <li>a <em>data service</em>, which is an abstract placeholder for &ldquo;concrete&rdquo; components
(compositions or task contexts).</li>
 <li>a <em>composition</em>, which represents a way to bind components together to form
a new component</li>
</ul>

<p>The first part of this documentation will guide you through <em>defining</em> these
components.</p>

<ul>
 <li><a href="task_contexts.html">task contexts</a> are automatically imported from oroGen descriptions.
Because of the <a href="index.html#naming_convertion">naming convertion rules</a>,
the Roby model that represents the oroGen task context xsens_imu::Task is
called XsensImu::Task.</li>
 <li><a href="compositions.html">compositions</a> are defined as subclasses of Syskit::Composition.</li>
</ul>

<pre><code class="language-ruby">class CompositionModelName &lt; Syskit::Composition
 &lt;definitions&gt;
end
</code></pre>

<ul>
 <li><a href="data_services.html">data services</a> are defined using the following code block. This block defines
a ServiceModelName <strong>Ruby module</strong> in the current module that represents the
service model.</li>
</ul>

<pre><code class="language-ruby">data_service_type "ServiceModelName" do
 &lt;definitions&gt;
end
</code></pre>

<p>The second part will tell you all about building systems using these parts.</p>

<h2 id="browsing">Browsing</h2>

<p>All models defined in Syskit can be browsed using the <code>syskit</code> command. It gives
all the information about the task contexts and types that are currently
installed in your rock environment, as well as about compositions and
components. Additionally, it shows where (file and line) a particular model is
defined and shows syntax or modelling errors. When you are developping new
models, simply press the &ldquo;reload&rdquo; button until you fixed all errors that showed up.</p>

<p>To run this tool, simply go into the bundle you are working on and run</p>

<pre><code>syskit browse
</code></pre>



</div>
</div>
</div>
