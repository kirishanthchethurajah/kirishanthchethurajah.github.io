---
layout: page
title: Runtime Setup
section: ROS/Rock bridge
---
<div class="content2">
<div class="content2-pagetitle">Runtime Setup</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>Currently, the Rock tooling is not able to start and configure ROS nodes by
itself. You still have to use the ROS tooling to manage your ROS nodes.</p>

<p>This page assumes that you are able to start ROS nodes using roslaunch. What we
will see is how to connect a live ROS system with Rock components, as well as
with the rest of the Rock tooling (such as e.g. vizkit)</p>

<h2 id="connecting-ros-nodes-and-rock-components">Connecting ROS nodes and Rock components</h2>

<p>To be able to connect ROS nodes with Rock components, you can just use the
typical startup Ruby scripts starting with</p>

<pre><code class="language-ruby">require 'orocos'
Orocos.initialize
</code></pre>

<p>For ROS integration to work, you will need to have setup the ROS_MASTER_URI environment
variable properly (note: the rock.ros package setup sets it up to the default
local master URI).
Also make sure that roscore is started beforehand. Otherwise ROS
integration will be disabled automatically: </p>

<pre><code>Orocos[WARN]: ROS integration was enabled, but I cannot contact
              the ROS master at http://localhost:11311, disabling
</code></pre>

<p>Once this is done, the ROS nodes can be resolved by the name service using their
ROS name:</p>

<pre><code class="language-ruby">ros_node = Orocos.name_service.get '/prosilica_driver'
</code></pre>

<p>The topics to which this node are subscribed are listed as input ports and the
ones it publishes at output ports:</p>

<pre><code class="language-ruby">ros_node.find_output_port('/prosilica/image_raw')
</code></pre>

<p>Alternatively, one can get a handle on a topic, and then publish or subscribe a
component port to it:</p>

<pre><code class="language-ruby">ros_topic = Orocos::ROS.topic('/prosilica/image_raw')
ros_topic.connect_to task.an_input_port
task.an_output_port.connect_to ros_topic
</code></pre>

<p>This will fail if the topic does not already exist. To create a topic by
publishing / subscribing a port, use #publish_on_ros and #subscribe_to_ros. Note
that these two methods work in case the topics do exist as well. What the method
above provides is protection against typos: it will fail if the topic does not
already exist.</p>

<pre><code class="language-ruby">task.an_input_port.subscribe_to_ros('/topic/name')
task.an_output_port.publish_on_ros('/topic/name')
</code></pre>

<p>In both cases, a default topic name of task_name/port_name can be used by not
specifying the topic name at all:</p>

<pre><code class="language-ruby">task.an_input_port.subscribe_to_ros
task.an_input_port.publish_on_ros
</code></pre>



</div>
</div>
</div>
