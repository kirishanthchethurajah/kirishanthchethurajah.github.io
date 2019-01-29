---
layout: page
title: Handling Geometry - the Transformer
section: Data Processing
---
<div class="content2">
<div class="content2-pagetitle">Handling Geometry - the Transformer</div>
<div class="content2-container line-box">
<div class="content2-container-1col">


<p>In the example used <a href="stream_aligner.html">for the stream aligner</a>, we introduced
the (very common) use case of having a sensor giving some geometrical
information in some (usually moving) frame, but the need to have this data in
another frame:</p>

<p class="align-center"><img src="stream_aligner_chain.png" alt="Example Processing Chain" /></p>

<p>In this example, the output of the &ldquo;Pointcloud&rdquo; processing should be expressed
in a frame that is centered on the body. Moreover, the laser filtering
processing is supposed to remove the body parts of the robot from the lidar
scan, which means that it should be able to express the position of the lidar
echoes in the body frame (or the position of the body parts in the lidar frame).</p>

<p>Since there is a moving servo between the body and the sensors, this requires a
bit of processing. Moreover, if one wants to build processing components that do
not require an intimate knowledge about each robot&rsquo;s kinematic structures, there
is a need for some infrastructure.</p>

<p>That&rsquo;s the job of the transformer:</p>

<ul>
<li>allow each processing component to specify the frame transformations that it
requires during its processing</li>
<li>provide runtime support to make sure that the required transformations are
made available to the components, regardless of the fact that they are static
or dynamic (i.e. require some connections to components that provide them).</li>
</ul>

<p>This page, as well as the next ones, will deal with explaining these issues.</p>



</div>
</div>
</div>
