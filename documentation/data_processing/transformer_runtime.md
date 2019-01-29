---
layout: page
title: ... at runtime
section: Data Processing
---
<div class="content2">
<div class="content2-pagetitle">  ... at runtime</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>This page is going to describe the runtime configuration interface of the
components that use the transformer oroGen plugin. It will then describe runtime
support for the transformer <strong>in the context of Ruby scripting</strong>, and data
display. The integration in the system management layer will come in the next
pages.</p>

<h2 id="component-interface">Component Interface</h2>
<p>Components that use the transformer oroGen plugin get the following
interface elements:</p>

<ul>
<li>normal stream aligner properties. Each aligned port has a <em>port_name</em>_period
property that allows to set its period. The transformer has a
transformer_max_latency property that allows to override the default set in
the transformer declaration block</li>
<li>a static_transforms property that allows to set the transformations that are
known and do not change at startup, and</li>
<li>a dynamic_transforms port that allows to send changing transformations to the
transformer. The data must be sent using the
<a href="/types/base/samples/RigidBodyState.html">base/samples/RigidBodyState</a> type.
Since the source and target frames are stored in the data structure, all
streams that are </li>
<li>finally, for each declared frame, a _frame_name_frame property that allows to
set the actual frame name for the component&rsquo;s internal frame.</li>
</ul>

<p>If, for any reasons, one wants to read dynamic transformations from a port
separate from dynamic_transforms, it is made possible by manually feeding the
data to the transformer C++ object. This <strong>must</strong> be done before the call to
TaskBase::updateHook</p>

<pre><code class="language-cpp">base::samples::RigidBodyState rbs;
while(_my_transform_input.read(rbs, false) == RTT::NewData)
 _transformer.pushDynamicTransform(rbs);
</code></pre>

<h2 id="transformation-specifications">Transformation Specifications</h2>
<p>The transformation specification file is a Ruby file that lists the known static
and dynamic transformations in a system. It is usually stored in a
config/transforms.rb file.</p>

<p>Static transformations are declared with</p>

<pre><code class="language-ruby">static_transform translation, rotation,
"source_frame_name" =&gt; "target_frame_name"
</code></pre>

<p>Translation and rotations are resp. specified with Eigen::Vector3d and
Eigen::Quaternion. The very useful Eigen::Quaternion.from_axis(angle, axis) call
is often used in these files. The translation or rotation can be omitted if they
are identities.</p>

<p>Dynamic transformations are declared with</p>

<pre><code class="language-ruby">dynamic_transform "producer_task_name.port_name",
"source_frame_name" =&gt; "target_frame_name"
</code></pre>

<p>Where <em>producer_task_name</em> and <em>port_name</em> refer to the data stream that defines
the required transformation. If the task has only one RigidBodyState output
port, the port name can be left empty.</p>

<p>For our laser filter example, a possible configuration file would look like:</p>

<pre><code class="language-ruby"># The frame names in this file are arbitrary !
static_transform Eigen::Vector3.new(0.3, 0, 0),
"Body" =&gt; "ServoFrom"
static_transform Eigen::Quaternion.from_angle_axis(1, Eigen::Vector3.UnitX),
"ServoTo" =&gt; "Lidar"
dynamic_transform "servo.transform_samples",
"ServoFrom" =&gt; "ServoTo"
</code></pre>

<h2 id="ruby-scripting">Ruby scripting</h2>
<p>When using the components in Ruby scripts, some automatic configuration can be
achieved.</p>

<p>Three bits are needed to configure the system properly:</p>

<ol>
<li>the transformation declaration file (see above)</li>
<li>proper configuration of the _frame_name_name properties on the components.
These properties are required for every frame that is required during
computation, and for the components that generate frame transformations (as
e.g. the servo driver compontn). In our example, we therefore need to setup
the laser filter and the servo.</li>
<li>declarations that tell in which frame the data produced by the tasks is
expressed in. This is not strictly required, but allows proper configuration
of the 3D display (video below).</li>
</ol>

<p>Finally:</p>

<pre><code class="language-ruby"># You need to initialize before you can use the transformer
Orocos.initialize
Orocos.transformer.load_conf('config/transforms.rb')
Orocos.run ... do
lidar = Orocos.name_service.get 'lidar'
filter = Orocos.name_service.get 'lidar_filter'
servo = Orocos.name_service.get 'servo'

# Tell in which frames is the data expressed. These are not
# arbitrary anymore: they MUST match the frame names listed
# in the transformer's
lidar.lidar_samples.frame = 'Laser'
filter.filtered_samples.frame = 'Laser'

# Properly setup frame names. These are not arbitrary anymore:
# they MUST match the frame names listed in the transformer's
# configuration files
filter.body_frame = "Body"
filter.input_frame = "Laser"
servo.source_frame = "ServoFrom"
servo.target_frame = "ServoTo"

lidar.lidar_samples.connect_to(filter.lidar_samples)

# Finalize transformer configuration (see below for explanations)
# For static transformations the task should not yet be configured
Orocos.transformer.setup(lidar, filter, servo)

lidar.configure
lidar.start
filter.configure
filter.start
servo.configure
servo.start

# Wait for ENTER on input
STDIN.readline
end
</code></pre>

<p>The Orocos.transformer.setup call provides the following additional features:</p>

<ul>
<li>it verifies that all required xxx_frame properties are set with frames that
exist in the transformer configuration files,</li>
<li>it connects the necessary frame producers to the dynamic_transformations
ports of the tasks that require it. In this example, it means that the
servo&rsquo;s transform_samples output is connected to the filter&rsquo;s
dynamic_transformations input,</li>
<li>it provides the necessary static transformations in the corresponding task
properties,</li>
<li>finally, it saves and broadcasts the system transformation configuration so
that it can be picked up by e.g. the 3D display</li>
</ul>

<p>TODO: video</p>



</div>
</div>
</div>
