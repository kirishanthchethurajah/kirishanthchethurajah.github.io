---
layout: page
title: Adding a Joystick into the Mix
section: Basics
---

<div class="content2">

<div class="content2-pagetitle">Control through a Virtual Joystick</div>

<div class="content2-container line-box">
<div class="content2-container-1col">


                  <h2 id="tutorial-info">Tutorial Info</h2>

<p>This tutorial will give you some hands-on experience on:</p>

<ul>
  <li>How to use a graphical widget to control the rolling rock.</li>
</ul>

<p><em>TODO Rewrite the tutorial using the task inspector</em></p>

<h2 id="finding-the-right-widget">Finding the right widget</h2>

<p><em>TODO Add as soon as it is integrated into the package directory</em></p>

<h2 id="integrating-it">Integrating it</h2>
<p>Now that we found the widget we want to use, we need to integrate it.
Again, we therefore copy the runscript from the <a href="500_simulate_a_robot.html">&lsquo;Simulate a Robot&rsquo; tutorial</a>
to a file called <em>rockTutorial2.rb</em> and modify it as follows. </p>

<p>We start by adding Vizkit to the section with the required packages:</p>

<pre><code class="language-ruby">require 'vizkit'
</code></pre>

<p>The <em>Orocos.run</em> block should be updated as follows:</p>

<pre><code class="language-ruby">## load and add the 3d plugin for the rock
Orocos.run 'rock_tutorial::RockTutorialControl' =&gt; 'rock_control' do

  rockControl = Orocos.name_service.get 'rock_control'
  rockControl.start

  ## Create a sample writer for a port ##
  sampleWriter = rockControl.motion_command.writer

  # get a sample that can be written to the port
  # If you know the sample type (here, base::MotionCommand2D),
  # an alternative syntax is
  #   sample = Types::Base::MotionCommand2D.new
  sample = sampleWriter.new_sample

  # create a widget that emulates a joystick
  joystickGui = Vizkit.default_loader.create_widget('VirtualJoystick')

  #show it
  joystickGui.show

  ## glue the widget to the task writer
  joystickGui.connect(SIGNAL('axisChanged(double, double)')) do |x, y|
    sample.translation = x
    sample.rotation = - y.abs() * Math::atan2(y, x.abs())
    sampleWriter.write(sample)
  end

  Vizkit.display rockControl.pose,
  :widget =&gt; Vizkit.default_loader.RigidBodyStateVisualization

  Vizkit.exec

end
</code></pre>

<p>The <em>joystickGui</em> part will open the desired widget and give us a handle to it.
Using the handle, we can register a code block in the run-loop that gets
executed whenever the widget emits a QT-Signal. Since this code needs a handle
to the <em>rockControl</em> task, we need the the <em>Orocos.name_service.get</em> line that
initializes <em>rockControl</em>.</p>

<p>Whenever the signal &lsquo;axisChanged&rsquo; is emitted by the widget, the
code inside the &lsquo;do / end&rsquo; is executed. One can think of it like a callback function. We use this &lsquo;callback&rsquo;
to modify a MotionCommand2D in respect to the axis values and write it to our <em>rockControl</em> task. Thus, we
provide the &lsquo;glue&rsquo;-code between the widget and the Task and also show the power of the scripting interface.</p>

<h2 id="run-it">Run it</h2>
<p>If you execute </p>

<pre><code class="language-text">ruby rockTutorial2.rb
</code></pre>

<p>two windows should pop up. One with our virtual joystick, the other one should be the well known visualization of the rock.
Clicking and dragging the joystick should also make the rock move.</p>



</div>
</div>
</div>
