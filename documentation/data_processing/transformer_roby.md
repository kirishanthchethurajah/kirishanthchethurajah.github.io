---
layout: page
title: ... in Syskit
section: Data Processing
---
<div class="content2">
<div class="content2-pagetitle">... in Syskit</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>So far, we have seen the following bits about the transformer:</p>

<ul>
 <li>the declaration, in oroGen, of the transformations required to process the data</li>
 <li>the transformer configuration file</li>
 <li>the runtime component configuration</li>
 <li>finally, additional frame information that can be provided at runtime to
allow proper display</li>
</ul>

<p>This page will deal with the usage of the transformer in Syskit, Rock&rsquo;s system
management layer. We will first see what Roby can do for you when using the
transformer, then how to extend the transformer models to allow full automatic
configuration and how to provide the runtime configuration.</p>

<p class="warning"><a href="../system_management_tutorials/1000_transformer.html">There is a corresponding
tutorial</a>, so we advise
you to follow it as well</p>

<p>The challenge here is to be able to configure <strong>and reconfigure</strong> a complete
system by providing the minimal amount of information, as always in Syskit.</p>

<h2 id="the-need-for-more">The need for more</h2>
<p>Let&rsquo;s take the same example than before: our laser filter (and, for now, only
the laser filter). Conceptually, the processing chain for that filter is:</p>

<p><img src="transformer_roby_initial.png" alt="Example processing chain" /></p>

<p>When doing Ruby scripting, one has to specify 4 frames:</p>

<ul>
 <li>the frame of the lidar_samples port</li>
 <li>the input and body processing frames of the laser_filter::Task</li>
 <li>the frame of the filtered_samples port</li>
</ul>

<p>However, one can notice that <strong>by construction</strong>, the frames of the
lidar_samples port, the input frame of the laser_filter::Task and of the
filtered_samples ports are all the same.</p>

<p>Moreover, since to get the input-to-body transformation, the laser filter
component will need the output transformation from the servo. The <strong>actual</strong>
needed network on our example system should therefore actually look like:</p>

<p><img src="transformer_roby_initial_with_servo.png" alt="Example processing chain with servo" /></p>

<p>However, the additional servo component is very specific to our system, and is
dependent on the laser filter configuration (a different assignation of frames
to input and body could lead to chains that do not require the servo output, or
chains that would require a different transformation producer).</p>

<p>What we will now see is:</p>

<ul>
 <li>how to describe the transformer setup better to reflect the links between
frames and ports</li>
 <li>how to select frames in the context of Roby controllers</li>
 <li>how dynamic transformation producers get added to the final network
automatically</li>
</ul>

<h2 id="additional-transformer-modelling">Additional transformer modelling</h2>
<p>What we have seen in the previous pages are the transformer specification that
are <strong>required</strong> for a component to work. I.e. how to declare the
transformations that our processing needs, and how to use them from the
component implementation.</p>

<p>What we are dealing with now is modelling that <strong>describes</strong> how the
component is implemented. I.e., the declarations that we will discover below
will <strong>not</strong> change the component behaviours. They are merely meant to describe
it.</p>

<p>These declarations can be added in either the component&rsquo;s oroGen file or, if
that file is not available or for convenience reasons, to Syskit . I.e. either
in laser_filter.orogen:</p>

<pre><code class="language-ruby">task_context "Task" do
 transformer do
   &lt;declarations&gt;
 end
end
</code></pre>

<p>or, in models/orogen/laser_filter.rb</p>

<pre><code class="language-ruby">class LaserFilter::Task
 transformer do
   &lt;declarations&gt;
 end
end
</code></pre>

<p class="warning"><strong>When should you do one or the other ?</strong>. One thing to consider, when adding
these declarations, is that by adding it to an oroGen project, you make this
project dependent on the drivers/orogen/transformer package. This is not an
issue when it is already the case, but might be undesirable when adding it to
tasks that otherwise do not require this dependency (such as, in our example,
the lidar and servo tasks).</p>

<p><strong>To associate a frame to a port</strong>, i.e. to tell the system that a particular port
is expressed in a particular frame, one adds</p>

<pre><code class="language-ruby">associate_frame_to_ports "frame_name", "port_name1", "port_name2"
</code></pre>

<p>where the ports can be both inputs and outputs. In our laser filter, the needed
declaration would be:</p>

<pre><code class="language-ruby">associate_frame_to_ports "input", "lidar_samples", "filtered_samples"
</code></pre>

<p>and in the lidar task, it would be:</p>

<pre><code class="language-ruby">associate_frame_to_ports "output", "lidar_samples"
</code></pre>

<p><strong>To associate a transformation to a port</strong>, i.e. to tell the system that a
particular port outputs a transformation, one adds:</p>

<pre><code class="language-ruby">transform_output "port_name", "source_frame" =&gt; "target_frame"
</code></pre>

<p>For instance, in the servo task, one would add:</p>

<pre><code class="language-ruby">transform_output "transform_samples", "lower" =&gt; "upper"
</code></pre>

<p>And now, the result:</p>

<p><img src="transformer_roby_complete_model.png" alt="Model with all transformation declarations" /></p>

<h2 id="transformer-configuration">Transformer configuration</h2>
<p>What we have so far is: a model of each of our component&rsquo;s frames, and how these
frames are linked to the transmitted data.</p>

<p>We&rsquo;re missing two things:</p>

<ul>
 <li>a transformer configuration file</li>
 <li>a way to tell Syskit which transformations are required in the running
network</li>
</ul>

<p>But, first things first, the transformer integration is currently disabled by default, so
you need to add the following line in config/init.rb (or uncomment it in bundles
created by rock-create-bundle)</p>

<pre><code class="language-ruby">Syskit.conf.transformer_enabled = true
</code></pre>

<p>The transformer when used in Syskit is configured in transformer blocks within
the <a href="../system/profiles.html">Syskit profiles</a>. For instance</p>

<pre><code class="language-ruby">profile 'P' do
 transformer do
 end
end
</code></pre>

<p>The non-dynamic part of <strong>the transformer configuration file</strong> is identical in
Syskit and in Ruby scripts. Assuming that a configuration file that does not
contain dynamic transformation statements is called
config/transforms.rb, one would load it with</p>

<pre><code class="language-ruby">profile 'P' do
 transformer do
   load 'config/transformer.rb'
 end
end
</code></pre>

<p>In Ruby scripts, dynamic transformation producers were declared as strings
following the task_name.port_name pattern. In Syskit, it must be a <a href="../system/subsystem_design.html">subsystem
declaration</a>, a component model or a device.</p>

<p>For instance, in our running example, one would do</p>

<pre><code class="language-ruby">profile 'P' do
 robot do
   device Dev::Actuators::Dynamixel, :as =&gt; 'dynamixel'
 end
 transformer do
   load 'config/transformer.rb'
   dynamic_transform dynamixel_dev, "lower" =&gt; "upper"
 end
end
</code></pre>

<h2 id="instanciating-networks-with-transformations">Instanciating networks with transformations</h2>

<p>What&rsquo;s left to do is introduce some frames in the system specification.</p>

<p>There are two main type of frames that usually need to be specified:</p>

<ol>
 <li>the frame in which the sensors produce their data (laser and servo in our
example)</li>
 <li>intermediate frames needed for processing  (the body frame in the laser
filter of our example).</li>
</ol>

<p>Sensor frames are specified in the device declarations in the robot block in the
profiles</p>

<pre><code class="language-ruby">profile 'P' do
 robot do
   device(Dev::Lidar).
     frame('laser')
   device(Dev::Servo).
     frame_transformation('lower_servo' =&gt; 'upper_servo')
 end
end
</code></pre>

<p>These frames get propagated in the network, and usually lead to having most of
the frame assignations resolved:</p>

<p><img src="transformer_roby_instanciated_without_body.png" alt="Transformer with most frame assignations resolved" /></p>

<p>Finally, one needs to specify in which frame the laser filter should do its
processing. This is given as the same time than one gives #use flags:</p>

<pre><code class="language-ruby">TutLaserFiltering.
 use_frame('body' =&gt; 'body')
</code></pre>

<p>Finally, one can see in the network above that the Servo::Task got added. This
is done <strong>automatically</strong> because the transformation produced by the servoing
task is required by the laser filter. The laser filtering composition does
<strong>not</strong> include the servo explicitely.</p>

<p>However, this also mean that you have no mean to give specific configurations to
the servo. To do that, you need to override the generic transformer
configuration when adding the laser filter composition:</p>

<pre><code class="language-ruby">TutLaserFiltering.
 use_frame('body' =&gt; 'body').
 use_transformation_producer(
       'lower_servo', 'upper_servo',
       Servo::Task.use_conf('default', 'sweeping')
 )
</code></pre>



</div>
</div>
</div>
