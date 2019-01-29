---
layout: page
title: Configuring the transformer
section: Setting up a New Project
---
<div class="content2">
<div class="content2-pagetitle">Configuring the transformer</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p><em>Note:</em> This is a rather advanced topic. Feel free to
<a href="../data_processing/transformer.html">understand</a>
how it works prior to configuring your own transformer.</p>

<p>When data is processed by your robotic system, there will usually be quite a
number of transformations between different frames, e.g. between a laser scanner
and the body-center of the robot. Rock&rsquo;s transformer handles the processing of
geometrical information, so that in each component, only the needed
transformations have to be specified, while transformations specified in other
components are made available during runtime.</p>

<p>Inside the bundle, the transformer file is located in <em>config/transforms.rb</em>.</p>

<p>Static transformations are declared as follows:</p>

<pre><code class="language-ruby">static_transform translation, rotation,
"source_frame_name" =&gt; "target_frame_name"
</code></pre>

<p>Translation and rotations are resp. specified with Eigen::Vector3d and
Eigen::Quaternion. The very useful Eigen::Quaternion.from_axis(angle, axis) call
is often used in these files. The translation or rotation can be omitted if they
are identities.</p>

<p>Dynamic transformations are declared as follows:</p>

<pre><code class="language-ruby">dynamic_transform "producer_task_name.port_name",
"source_frame_name" =&gt; "target_frame_name",
</code></pre>

<p>Where <em>producer_task_name</em> and <em>port_name</em> refer to the data stream that defines
the required transformation. If the task has only one RigidBodyState output
port, the port name can be left empty.</p>

<p>An example for a configuration file might look as follows:</p>

<pre><code class="language-ruby"># The frame names in this file are arbitrary !
static_transform Eigen::Vector3d.new(0.3, 0, 0),
"Body" =&gt; "ServoFrom"
static_transform Eigen::Quaternion.from_angle_axis
(1, Eigen::Vector3d::UnitX),
"ServoTo" =&gt; "Lidar",
dynamic_transform "servo.transform_samples",
"ServoFrom" =&gt; "ServoTo",
</code></pre>

<p>As you can see, the <em>transforms.rb</em> file contains the global information about
which transformation is specified in which task. Look
<a href="../data_processing/transformer_orogen.html">here</a>
for more information about how transformations are specified in oroGen
components.</p>


</div>
</div>
</div>
