---
layout: page
title: Devices
section: System Management
secondsection: system
---
<div class="content2">
<div class="content2-pagetitle">Devices</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>In robot software, devices are very specific things. All the devices form the
interface of the software with the external world. Moreover, in a
component-oriented system like the one we are manipulating in Syskit, devices
are <strong>unique</strong>: while it is possible to instanciate multiple image processing
components, a robot cannot arbitrarily spawn a new camera.</p>

<p>For these reasons, devices have a specialized place in Syskit as well. This
is because:</p>

<ul>
<li>devices are <strong>unique</strong>, for the reason above</li>
<li>devices are the <strong>source</strong> of all data. Therefore, they have a
special place when looking at the dataflow (more on that <a href="dataflow_dynamics.html">in a later
section</a>.</li>
</ul>

<p>In Syskit, <strong>devices</strong> are defined as a special kind of <a href="data_services.html">data
service</a>. You need to know about data services first.</p>

<p>We will start about how these things are modelled. Note that some of the
sections on this page are marked as advanced. It is fine to skip them on a first
reading, only to come back later when they are needed.</p>

<h2 id="declaring-devices">Declaring devices</h2>
<p>Device models are specialized forms of data services. They are declared in the
same way than data services, but instead using the device_type stanza instead of
the data_service_type one.</p>

<div class="block">
<p><strong>Generation</strong> A template file for a new device, which follows syskit naming and
filesystem convention, can be generated with</p>

<pre><code>syskit gen dev name/of_device
</code></pre>

<p>For instance, running the following command in the rock bundle</p>

<pre><code>syskit gen dev cameras/prosilica
</code></pre>

<p>will generate the Rock::Devices::Cameras::Prosilica device type in
models/devices/cameras/prosilica.rb and update models/devices.rb as well as
models/devices/cameras.rb to require the new file(s).</p>
</div>

<p><strong>Naming</strong> Devices are defined under the Devices module in the models/devices/
folder. The goal, when declaring devices, is to refer to the device as precisely
as possible. I.e. one would never declare a Camera device. Instead, it would
declare a FirewireCamera or a ProsilicaCamera. For instance:</p>

<pre><code class="language-ruby">device_type "Camera" do
provides Services::ImageProvider
end
</code></pre>

<p>In Rock, all devices are categorized within the Devices namespace. See
models/devices/categories.rb in the rock bundle for a common categorization</p>

<h2 id="communication-busses">Communication Busses</h2>
<p>A special type of device is a <strong>communication bus</strong>. The busses are meant to
provide multiplexing/demultiplexing for other devices. They are handled directly
by Syskit, which has major advantages:</p>

<ul>
<li>it avoids the need to create a composition per device that is attached to the
bus (the solution one would have so far).</li>
<li>once support for a given type of communication bus is written, any device
conforming to the Syskit combus conventions can use it.</li>
<li>the combus-to-driver relationship is setup in a meaningful way (i.e. com bus
is started before the driver and stopped afterwards).</li>
</ul>

<p>A communication bus type is declared with</p>

<pre><code class="language-ruby">com_bus_type('Canbus', message_type: '/canbus/Message')
</code></pre>

<p>where the message type is the type of the input/output ports that are used to
communicate with that type of communication bus. It is customary to define the
busses under the Dev::Bus namespace (see models/devices/categories.rb in the
rock bundle for a common hierarchy).</p>

<div class="block">
<p><strong>Generation</strong> A template file for a new com bus, which follows syskit naming and
filesystem convention, can be generated with</p>

<pre><code>syskit gen bus name/of_bus
</code></pre>

<p>For instance, running the following command in the rock bundle</p>

<pre><code>syskit gen dev schilling_dts
</code></pre>

<p>will generate the Rock::Devices::Bus::SchillingDts bus type in
models/devices/bus/schilling_dts.rb and update both models/devices/bus.rb as
well as models/devices.rb to require the new file(s).</p>
</div>

<h2 id="declaring-device-drivers">Declaring Device Drivers</h2>
<p>In order to use a device at runtime, one has to have a <strong>device driver</strong>, that
is a task context that is declared as being able to interface with that device.</p>

<p>This relationship is declared with the driver_for stanza. For instance, in
models/orogen/camera_prosilica.rb, one writes:</p>

<pre><code class="language-ruby">class OroGen::CameraProsilica::Task
driver_for Rock::Devices::CameraProsilica
end
</code></pre>

<p>To import the device definitions from other bundles, one has to do:</p>

<pre><code class="language-ruby">require 'rock/models/devices/camera_prosilica'
</code></pre>

<p>i.e. prefix the device file path with the bundle&rsquo;s name.</p>

<h2 id="robot-definitions">Robot Definitions</h2>
<p>Device and combus models are used to describe the interface to the robot
hardware. This is done <a href="profiles.html">in profiles</a> using a robot block:</p>

<pre><code class="language-ruby">profile 'MyRobot' do
robot do
 device(Devices::CameraProsilica, as: 'left_camera')
 device(Devices::CameraProsilica, as: 'right_camera')
end
end
</code></pre>

<p>Once defined, the devices can be referred to in the rest of the profile with the
devicename_dev form (e.g. left_camera_dev above). For instance:</p>

<pre><code class="language-ruby">syskit_profile 'MyRobot' do
robot do
 device(Devices::CameraProsilica, as: 'left_camera')
 device(Devices::CameraProsilica, as: 'right_camera')
end
define 'left_image', Compositions::ImageProcessingPipeline.use(left_camera_dev)
end
</code></pre>

<p>If only one device driver is available for a given device, is is automatically
selected. Otherwise, one has to select it explicitly in the device declaration:</p>

<pre><code class="language-ruby">device Devices::CameraProsilica, using: OroGen::AlternativeDriver::Task
</code></pre>

<p>We will see later on that the device model can hold important information about
the device, such as the expected update rate. This information can be accessed
from <a href="task_contexts.html#extend">the task context&rsquo;s configure method</a> with:</p>

<pre><code class="language-ruby">class OroGen::CameraProsilica::Task
def configure
 super
 robot_device # =&gt; the Syskit::Robot::MasterDeviceInstance
              #    object that describes the device this
              #    component is interfaced with.
end
end
</code></pre>

<h2 id="combus-support-advanced">Combus Support <strong>(Advanced)</strong></h2>
<p>Communication bus support in Syskit relies on the following conventions:</p>

<ul>
<li>the device driver must have at least one input or output of the combus
message type (it can obviously have both, but having none is an error)</li>
<li>if the device driver expects an output, the communication bus driver <strong>may</strong>
either have a single input port of the combus message type, or declares a
<a href="../orogen/task_interface.html#dynamic-ports">dynamic input port</a> of the
message type</li>
<li>if the device driver expects an input, the communication bus driver <strong>must</strong>
declare a <a href="../orogen/task_interface.html#dynamic-ports">dynamic output port</a>
of the combus message type</li>
</ul>

<p>The convention that the combus driver <strong>must</strong> follow for dynamic ports is that
the ports should be created at configuration time, and should be named
wdevice_name and device_name, where &lsquo;device_name&rsquo; is the name of the attached
device. This configuration is usually done in the <a href="task_contexts.html#extend">combus configure
method</a></p>

<p>For instance, let&rsquo;s consider the combus declaration from above</p>

<pre><code class="language-ruby">com_bus(Devices::Bus::Canbus, :as =&gt; 'can0')
through 'can0' do
device(Devices::Actuators::Hbridge, :as =&gt; 'actuators').
end
</code></pre>

<p>The actuator device driver need both input and output through the communication
bus. The driver therefore declares an input port and an output port of the
/canbus/Message type. These names are arbitrary.</p>

<p>The <a href="/tasks/canbus::Task.html">canbus component</a> declares a static input port
of the /canbus/Message type. This is going to be used for all data that should
be sent to the bus. For the bus-to-driver part, it declares a dynamic output
port with</p>

<pre><code class="language-ruby">dynamic_output_port /.*/, "canbus/Message"
</code></pre>

<p>The usage of the hbridge driver attached to the can combus is therefore
<strong>valid</strong> from Syskit&rsquo;s point of view, and the network generation will
succeed.</p>

<p><strong>At runtime</strong>, Syskit will require the combus component to create, during
configuration, an output port of the same name than the device (i.e.
&ldquo;actuators&rdquo;). This creation is done in the combus configure method. For instance
(forget about the can_id thing for now, this is a quite advanced topic that will
be explained later).</p>

<pre><code class="language-ruby">class OroGen::Combus::Task
def configure
 super
 each_attached_device do |dev|
   can_id, can_mask = dev.can_id
   name = dev.name
   Robot.info "#{bus_name}: watching #{name} on 0x#{can_id.to_s(16)}/#{can_mask.to_s(16)}"
   orogen_task.watch(name, can_id, can_mask)
 end
end
end
</code></pre>



</div>
</div>
</div>
