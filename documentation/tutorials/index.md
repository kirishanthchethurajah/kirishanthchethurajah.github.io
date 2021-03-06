---
layout: page
title: Introduction
section: Basics
---

<div class="content2">
<div class="content2-pagetitle">Introduction</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="rock-tutorials">Rock Tutorials</h2>
<p>Welcome to the Rock tutorials! The tutorials are intended to allow practitioners and developers in the robotics field to start working with the Robot Construction Kit.
To get the most out of the tutorials, you should be familiar with basic software development in C++. The tutorials will not teach you C++ or software development in general, but they provide you with a guideline for developing components within Rock. </p>

<h2 id="what-to-expect-from-the-basic-tutorials">What to expect from the basic tutorials</h2>
<p>The tutorials are structured as follows:
The first set of tutorials covers the basics for working with Rock, and will teach you on how to create a library, embed into an Orocos component using orogen, and finally deploy it, i.e. build and run.
Further, the tutorials will teach you how to examine your data, how to log data from your running components and view them afterwards.
The complete set is wrapped up by a holistic example &lsquo;Rolling Rock&rsquo;.</p>

<h2 id="quick-start">Quick Start.</h2>
<p>In order to start with the tutorial, you require a minimal but working
installation of Rock. Just follow the installation procedure as described in the
<a href="../installation.html">step-by-step installation guide</a>.</p>

<p>To check if you are ready for your first tutorial, open your console and enter &lsquo;which rock-create-lib&rsquo; and verify that the command returns a full path to the &lsquo;rock-create-lib&rsquo;-script. </p>

<h2 id="tutorials-outline">Tutorials Outline</h2>

<p>The tutorials work along a typical Rock-workflow, and will give you a guideline on how to approach building software for your robotic system.
The typical workflow starts with implementing functionality in a library, allowing for a framework independent implementation, and proceeds further to generating an orogen (thus framework specific) component, and gives you some details on how to enhance your data analysis using logging and data visualization (<a href="../orogen/index.html">more</a>).</p>

<p>The showcase example allows you to repeat a more complex example to deepen your knowledge of how to use Rock, while you can still pick dedicated topics from the advanced tutorials. </p>

<p><strong>Basics</strong></p>

<p>The basic tutorials cover how to get started: Create new code, integrate it into
the build system and then into components.</p>

<ul>
  <li><a href="100_basics_create_library.html">Create a C++ library package</a></li>
  <li><a href="110_basics_create_component.html">Create a component</a></li>
  <li><a href="120_basics_configure_component.html">Configure your components</a></li>
  <li><a href="130_basics_connect_components.html">Run the components together</a></li>
</ul>

<p>If you want to know how to find and install packages, have a look at the following:</p>

<ul>
  <li><a href="190_installing_packages.html">Installing packages</a></li>
</ul>

<p>How to use logging:</p>

<ul>
  <li><a href="200_display_logging_and_replay.html">Displaying data, logging and replay</a></li>
</ul>

<p><strong>Showcase Example</strong></p>

<p>This example covers, in principle, the same subjects as the basic tutorials,
but with a more complex (and more realistic) example. You will learn how to
browse and find components in the Rock component collection.</p>

<ul>
  <li><a href="500_simulate_a_robot.html">Simple simulation of a robot</a> </li>
  <li><a href="510_joystick.html">Integrating a Joystick</a></li>
  <li><a href="520_virtual_joystick.html">Integrating a Virtual Joystick (for those that don&rsquo;t have a physical
one)</a></li>
</ul>

<h2 id="installing">Installing the tutorial results</h2>
<p>In case you don&rsquo;t want to write all the packages yourself, the Rock tutorials have been packaged so that you can easily retrieve them.
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
