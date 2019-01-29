---
layout: page
title: Adding a Joystick into the Mix
section: Basics
---

<div class="content2">

<div class="content2-pagetitle">Adding a Joystick into the Mix</div>

<div class="content2-container line-box">
<div class="content2-container-1col">


              <h2 id="tutorial-info">Tutorial Info</h2>

<p>This tutorial will give you some hands-on experience on:</p>

<ul>
<li>How to find a &lsquo;standard&rsquo; Rock package that provides a port X,</li>
<li>how to install the package and</li>
<li>how to create a small control loop by attaching a Joystick to our rolling rock task.</li>
</ul>

<h2 id="finding-a-standard-task-that-provides-a-port-x">Finding a standard task that provides a port X.</h2>

<p>We are using an existing task of the rock-robotics framework and the task from the previous
tutorial, so we don&rsquo;t have to implement anything.</p>

<p>In order to get our control, we need a task that supplies us with &lsquo;base::commands::Motion2D&rsquo; (note that this
type is equivalent to &lsquo;base::MotionCommand2D&rsquo; which you will find in some older components).
For that it is recommended to take a look into the <a href="../../package_directory.html">package directory</a>.
You will have two options there:</p>

<ul>
<li>Look for a specific orogen package in the packages section. For instance, you can search for
&ldquo;joystick orogen&rdquo;.</li>
<li>Look for tasks that produce the type you require (here &lsquo;/base/commands/Motion2D&rsquo;) by going into
the oroGen types section, selecting the type and looking at the &lsquo;Produced by&rsquo; section at the
top of the page.</li>
</ul>

<p>The package directory gives you a general overview of available &lsquo;standard&rsquo; packages and types.
For our purpose, we need to look into the subcategory &lsquo;oroGen Types&rsquo;. This category shows all
available types that are exported by any task in the standard rock packages. As we are interested
in &lsquo;base::commands::Motion2D&rsquo;, we open the page about this type.</p>

<p>As we want to integrate a joystick, we search for controldev::JoystickTask
in the subcategory &lsquo;oroGen tasks&rsquo; &ndash; and find it defined in &lsquo;drivers/orogen/controldev&rsquo;.
This task produces &lsquo;base::commands::Motion2D&rsquo;.</p>

<p>In order to install the package to your installation, either do</p>

<pre><code class="language-text">amake drivers/orogen/controldev
</code></pre>

<p>which will install the current version of the package, but not update / build it
later. To permanently integrate it into your whole installation, you have to <a href="100_basics_create_library.html#add-to-manifest">edit the
manifest file</a>.</p>

<h2 id="integration-of-the-task">Integration of the task</h2>

<p>Now that we have found a task that supplies the needed motion commands, we need to integrate it into
our run script. Therefore we modify the last runscript by adding a joystick driver to it.
As we know from the package directory, the &lsquo;controldev&rsquo; package provides a tasks for our purpose. To find
out which exact task we can use, we call</p>

<pre><code class="language-text">oroinspect -t controldev
</code></pre>

<p>This returns us an output of every installed task that is defined controldev.
The interesting part of the output is in our case this :</p>

<pre><code>==========================================================
Task name:  controldev::JoystickTask
defined in controldev
---------------------------------------------------------- g
------- controldev::JoystickTask ------
A Task that provides a joystick driver
subclass of controldev::GenericTask (the superclass elements are displayed below)
Ports
[out]four_wheel_command:/controldev/FourWheelCommand
[out]motion_command:/base/commands/Motion2D
[out]raw_command:/controldev/RawCommand
[out]speed_command:/base/SpeedCommand6D
[out]state:/int32_t
No dynamic ports
Properties
device:/std/string: Path to the joystick device
maxRotationSpeed:/double: Maximum rotation speed in rad/s
maxSpeed:/double: Maximum translation speed in m/s
minRotationSpeed:/double: Minimum rotation speed in rad/s
minSpeed:/double: Minimum translation speed in m/s
No attributes
No operations
</code></pre>

<p>This output tells us that there is a task &lsquo;controldev::JoystickTask&rsquo;, which has an
output port with the name &lsquo;motion_command&rsquo; of the type &lsquo;/base/commands/Motion2D&rsquo;.
So it fits exactly our needs.</p>

<p>Knowing this, we can now modify the script from the previous tutorial (copy it
to <em>scripts/rockTutorial2.rb</em>):</p>

<pre><code class="language-ruby">require 'orocos'
require 'readline'
include Orocos

## Initialize orocos ##
Orocos.initialize

## Execute the task ##
Orocos.run 'rock_tutorial::RockControlTutorial' =&gt; 'rock_tutorial_control',
 'controldev::JoystickTask' =&gt; 'joystick' do
g
## Get the specific task context ##
rockControl = Orocos.name_service.get 'rock_tutorial_control'
joystick = Orocos.name_service.get 'joystick'

## Connect the ports ##
joystick.motion_command.connect_to rockControl.motion_command

## Set some properties ##
joystick.device = "/dev/input/js0" # this might be another port

## Configure the tasks ##
joystick.configure

## Start the tasks ##
joystick.start
rockControl.start

Readline::readline("Press Enter to exit\n") do
end
end

</code></pre>

<p>This script is quite the same as the script from
<a href="./500_simulate_a_robot.html">the previous tutorial</a>, but with some
improvements. As you can see, we execute a second Task named &lsquo;joystick&rsquo;,
besides the &lsquo;rock_tutorial&rsquo; task.</p>

<p>Furthermore, we acquire a handle to the JoystickTask. Then we connect the output port &lsquo;motion_command&rsquo;
of the task &lsquo;joystick&rsquo; to the equally named input port of the task &lsquo;rockControl&rsquo;. The task &lsquo;joystick&rsquo; of course uses the
joystick input to generate motion commands. In the next step, we could set a property named &lsquo;device&rsquo; of the task &lsquo;joystick&rsquo;.
Like that, we can set a string which is the local device of the joystick on your machine. Next, we configure the &lsquo;joystick&rsquo;
task with the command &lsquo;joystick.configure&rsquo;. Finally, we start all the tasks.</p>

<h2 id="run-it">Run it</h2>
<p>You can now simply run it by executing</p>

<pre><code class="language-text">    ruby rockTutorial2.rb
</code></pre>
<p>And in another console:</p>

<pre><code class="language-text">    rock-display rock_tutorial_control
</code></pre>
<p>Again, with a right-click on the <em>pose</em> port, you can start the visualization and enjoy commanding around the robot with the joystick.</p>


</div>
</div>
</div>
