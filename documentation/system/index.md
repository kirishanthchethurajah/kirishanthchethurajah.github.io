---
layout: page
title: Introduction
section: System Management
secondsection: system
---
<div class="content2">
<div class="content2-pagetitle">Introduction</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p class="note">While one can use Syskit, Rock&rsquo;s system management layer, without knowing the
underlying execution framework, Roby, reading both the
<a href="../../api/tools/roby/concepts/index.html">concept</a> section
of the Roby user&rsquo;s guide, and followed the
<a href="../../api/tools/roby/tutorial/index.html">tutorial</a> in the
same guide become beneficial when the complexity of your application grows.</p>

<h2 id="advanced-system-deployment">Advanced System Deployment</h2>

<p>Roby is a plan management / supervision system. The Rock toolchain offers
a Roby plugin that allows to manage Orocos component networks in Roby, thus
leveraging its supervision capabilities. See <a href="/api/tools/roby">the Roby user&rsquo;s
guide</a> for details.</p>

<p>In practice, what does this plugin offers ?</p>

<ul>
<li>have a model-based component deployment. Deploying medium-sized networks of
components (&gt; 10) is often hard to do right. It is even harder to be sure
that modifications to the component interfaces will not break the
deployments.</li>
<li>have a model-based adaptation of the component network. While it is hard to
deploy medium-sized component networks, it is even harder to modify the
networks while still being sure that you don&rsquo;t break anything.</li>
</ul>

<h2 id="tutorials">Tutorials</h2>

<p><a href="../system_management_tutorials/index.html">The tutorials</a> are a great introduction to the
plugin&rsquo;s concepts and development workflow.</p>

<h2 id="naming_convertion">Naming conventions</h2>
<p>In Ruby, the language in which both oroGen and Syskit are
implemented, the norm is to use CamelCase to write class names. An actual
constraint that is part of the language is that the class names <strong>must</strong> start
with an uppercase letter.</p>

<p>To match this convention, the system management layer, when needed, converts
snake_case names to CamelCase. For instance:</p>

<pre><code>xsens_imu &gt; XsensImu
iodrivers_base &gt; IodriversBase
</code></pre>

<p>A reminder about this convertion will be present in the documentation whenever
it applies.</p>

<h2 id="models-classes-and-instances">Models, classes and instances</h2>
<p>In the Roby system, models and instance of the models are mapped to Ruby&rsquo;s
classes and objects in a very straightforward manner: a model is represented as
a class or module (depending of what kind of thing this model describes), and an
instance of a model is an object of the corresponding class.</p>

<p>For instance, the Roby model that represents a xsens_imu::Task task context is a
XsensImu::Task class, which is a subclass of Syskit::TaskContext. The list of
ports defined on all xsens_imu::Task task contexts can then be obtained with
XsensImu::Task.each_input_port for instance.  At runtime, running
xsens_imu::Task task contexts are then represented by objects of class
XsensImu::Task, i.e. running_xsens_imu_task.class == XsensImu::Task.</p>



</div>
</div>
</div>
