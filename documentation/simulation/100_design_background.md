---
layout: page
title: Design Background
section: Simulating a Robot
---
<div class="content2">
<div class="content2-pagetitle">Design Background</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>The General design of the Mars Integration of Rock follows the component-based design idea.
Each Rock-Task is a <a href="../../api/simulation/mars/doc/d8/dd8/tutorial_basic_plugin.html">MarsPlugin</a>
This provides low-level access to the underlying simulation instances. Another
benifit is the syncronized access to the data structures without the need for
locking. </p>

<p class="warning"><strong><em>Important</em></strong>: The drawback on this approach is that all simulation tasks need to be in
the same deployment. Here, the MarsPlugin uses a singleton design pattern.</p>

<p>Each plugin provides the functionality that on a real system would be provided by the real hardware.
This could be an actuator driver, IMU, sonar, laserscanner and so on. Because of this clear separation
the simulation could be directly exchanged with the real robot and vice-versa. The simulation plugins
provides the same functionality as the real-drivers; the same data-types are used. This makes
the switch between simulation and real robot very smooth for Rock-Users.</p>

<p>Nevertheless, there are of course specific modules providing mixed functionalities or providing
additional or even reduced functionality depending on the capabilities of the
simulated device and the need for abstraction. However, the idea is to keep the
tasks which represent a device as close as possible to the driver for the physical device.</p>



</div>
</div>
</div>
