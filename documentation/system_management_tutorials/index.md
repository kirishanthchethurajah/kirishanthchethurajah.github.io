---
layout: page
title: Introduction
section: System Management
secondsection: Tutorials
---

<div class="content2">
<div class="content2-pagetitle">Introduction</div>
<div class="content2-container line-box">
<div class="content2-container-1col">


<p>Up to now, you have been writing down detailed instructions on how to bind
components together using a Ruby script.
After this tutorial, you will be able to use the modeling capabilities of Rock&rsquo;s system management layer
which allows you to <em>describe</em> the system you want to run and let the system management layer do the rest of the work,
such as connecting components.
There are multiple benefits to running systems using such a <em>model-based layer</em>. Among those:</p>

<ul>
  <li>Having a system <em>monitoring</em>, i.e. the ability to detect and represent
errors. No more silent errors.</li>
  <li>Extension of component networks: Quite often, a system needs not only <em>functional</em>
components, but also <em>support</em> components (think: diagnostic components,
communication bus support, hardware triggering readers, &hellip;). The model-based
layer allows you to focus on your functional components and let the tooling automatically
handle the support components.</li>
  <li>Online system adaptation: The ability to reconfigure individual components or even
a complete <em>network</em> of components at runtime.</li>
  <li>Encoding of complex behaviour: Building of high-level &ldquo;programs&rdquo; which rely on (networks or compositions of)
simple components.</li>
  <li>&ldquo;Think local, act global&rdquo;: Managing a network even as small as 15+ components
is a challenge. Rock&rsquo;s system management layer allows you to decompose tasks and problems and
think in small pieces, while the tooling takes care of generating the complete network of components.</li>
</ul>

<p>This series of tutorials will introduce you to Rock&rsquo;s advanced system management
tools. This tutorial builds upon the results of the previous tutorials, and in
particular <a href="../tutorials/500_simulate_a_robot.html">Simulate a Robot</a> and
<a href="../tutorials/510_joystick.html">Adding a Joystick into the Mix</a>, or that you at least installed
the tutorial results. See the bottom of <a href="../tutorials/index.html">this page</a> for
instructions on how to do so.</p>

<p>The tutorial itself will use Rock&rsquo;s system management layer to perform the
same task than was previously done using a Ruby script. Further tutorials will
then introduce some tooling that this layer offers, and go towards examples with more
complex systems.</p>



</div>
</div>
</div>
