---
layout: page
title: Usage Simulation
section: Simulating a Robot
---
<div class="content2">
<div class="content2-pagetitle">Usage Simulation</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="setting-up-the-system">Setting up the System</h2>
<p>To build a simulation of your system you first need the enviorment for the system and the system itself.
To crete a scene please refer to the <a href="../../api/simulation/mars/doc/d5/dc7/tutorial_basic_modeling.html">Mars Documentation</a></p>

<p>The next step is to figure out which sensors you need and which tasks they require in turn.
For this, see the <a href="../../api/simulation/orogen/mars_core/">mars_core</a> package.</p>

<p>Currently Rock provides support for:</p>

<ul>
<li>IMU Sensors</li>
<li>AUV Motions (based on the Fossen-Model)
Provides movement prediction within the simulation based on the prediction
of a fossen model.</li>
<li>laser scanners</li>
<li>motors (including joints)</li>
<li>cameras (including time-of-flight cameras)</li>
</ul>

<p>You have to Deploy all needed Tasks within one Deployment, because the underlaying access
is based on a singleton design pattern. After you colleded and deployed all tasks the base-setup is finished.</p>

<h2 id="configuring-plugins">Configuring Plugins</h2>
<p>The simulation tasks aim to provide the same functionality as the real sensors. Nevertheless, they
are often limited in their capabilities. The most important fact to know is that for almost every plugin,
you have to configure the referencing Rock node in the scene file. Each plugin has to be provided with the information on
which node needs to be controlled and which sensor to be attached respectively. The names of the Rock nodes are specified
when creating the scene. The second important fact is that each plugin node must be instanciated AFTER the core-simulation task is configured,
otherwise the configuration will fail. </p>

<p>The plugins are continuously checking if the core-simulation is still available, in case of a shutdown
each node switches to the error_state &ldquo;CONNECTION_LOST&rdquo;, which means that the whole simulation has to be restarted.</p>



</div>
</div>
</div>
