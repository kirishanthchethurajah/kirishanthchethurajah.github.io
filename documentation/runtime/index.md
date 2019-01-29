---
layout: page
title: Introduction
section: Running Components
---
<div class="content2">
<div class="content2-pagetitle">Introduction</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="running-components-using-ruby">Running components using Ruby</h2>

<p>Rock includes the orocos.rb library, which allows you to control Orocos/RTT
components from Ruby, i.e. to set them up, start/stop and monitor them.</p>

<p>This documentation assumes that you have a basic knowledge of what the Orocos/RTT is, and about the
Orocos/RTT component model. If you do not, then you can find information in the
documentation of <a href="../orogen/index.html">the oroGen component generator</a> and the
<a href="http://orocos.org/rtt">Orocos/RTT itself</a>.</p>

<p class="warning">While the orocos.rb bindings do <em>not</em> require you to use the oroGen code
generator, some functionality will not be available if you don&rsquo;t. Moreover,
orocos.rb uses the CORBA interface to the modules, so you need your deployments
to use CORBA (this is the default in oroGen).</p>

<p>The next page is a step-by-step on the basics of how to start modules, set them
up, build the communication network and stop everything. Then, the rest of the
guide will present all the available functionality in more details.</p>

<h2 id="tutorials">Tutorials</h2>
<p>The main Rock tutorials are a great introduction to the subject of running
components using Ruby. <a href="../tutorials/index.html">Check them out !</a></p>


</div>
</div>
</div>
