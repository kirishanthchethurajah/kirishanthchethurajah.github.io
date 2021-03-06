---
layout: page
title: Simulate a Robot
section: Basics
---

<div class="content2">

<div class="content2-pagetitle">Simulate a Robot</div>

<div class="content2-container line-box">
<div class="content2-container-1col">


<h2 id="abstract">Abstract</h2>
<p>This tutorial is the basis for the followup tutorials in this section. Before you try this tutorial,
you should have worked through the basic tutorials. </p>

<p>In this tutorial, we will create a library for simulating a &lsquo;Rock&rsquo;-Robot.
Further we will wrap it into an orocos task and fire it up. </p>

<h2 id="implementation">Implementation</h2>

<h3 id="create-a-new-library">Create a new library</h3>
<p>We start by entering the tutorials folder and creating a library named &lsquo;rock-tutorial&rsquo; by using the &lsquo;rock-create-lib&rsquo; command. </p>

<pre><code class="language-text">cd ~/dev/tutorials
rock-create-lib rock_tutorial
</code></pre>

<p>The required dependencies for our library are &lsquo;base/types&rsquo; and &lsquo;gui/vizkit3d&rsquo;.
See <a href="100_basics_create_library.html">the &ldquo;Creating libraries&rdquo; tutorial</a> for a
precise explanation of adding dependencies to a Rock library.</p>

<p>The automatically generated dummy files can be removed as explained in the note
of <a href="100_basics_create_library.html#creating-libraries">the &ldquo;Creating libraries&rdquo; tutorial</a>.</p>

<p>This should give us an folder containing the following files:</p>

<pre><code class="language-text">CMakeLists.txt  INSTALL  LICENSE  manifest.xml  README  src
</code></pre>

<p>Your <em>src/CMakeLists.txt</em> text file should be updated as follows:</p>

<pre><code class="language-text">rock_library(rock_tutorial
 SOURCES RockControl.cpp
 HEADERS RockControl.hpp
 DEPS_PKGCONFIG base-types)
</code></pre>

<p>Afterwards, create a new class in the src folder named &lsquo;RockControl&rsquo;, i.e. <em>RockControl.cpp</em>
and <em>RockControl.hpp</em>.</p>

<p>This class will contain the (very basic) simulation logic for our Rock-Robot. Therefore it will calculate new positions
of our robot given movement commands and a time step. </p>

<p>This is the content of the header file (<em>RockControl.hpp</em>):</p>

<pre><code class="language-cpp">#ifndef ROCKCONTROL_H
#define ROCKCONTROL_H

#include &lt;base/commands/Motion2D.hpp&gt;
#include &lt;base/samples/RigidBodyState.hpp&gt;

namespace rock_tutorial {

class RockControl
{

public:
   RockControl();
   virtual ~RockControl();

   base::samples::RigidBodyState computeNextPose(const double &amp;deltaTime,
     const base::commands::Motion2D &amp;command);

private:
   /**
   * Wraps angles into the interval between -PI and PI.
   */
   void constrainAngle(double&amp; angle);

   /**
   * This method constrains the relative rotation and
   * translation velocities of a 2D motion command.
   * Rotation should be clamped to the interval between -PI and PI.
   * Translation should be clamped to the interval between -10 and 10.
   */
   void constrainValues(base::commands::Motion2D&amp; motionCommand);

   /**
   * Current Position and orientation of the rock
   **/
   base::samples::RigidBodyState currentPose;

   /**
   * Current heading of the rock
   **/
   double currentHeading;

   /**
   * Current speed of the rock
   **/
   double currentRoll;

};

}

#endif // ROCKCONTROL_H
</code></pre>

<p>In the source file (<em>RockControl.cpp</em>), include the header
file and declare the namespace <em>rock_tutorial</em>.</p>

<p>The constructor and destructor should look as follows:</p>

<pre><code class="language-cpp">RockControl::RockControl() {
 currentHeading = 0.0;
 currentRoll = 0.0;
 currentPose.position = base::Vector3d(0,0,0);
}

RockControl::~RockControl() {
}
</code></pre>

<p>The comments in the header file should help you to implement the functionality
of the methods <em>constrainAngle</em> and <em>constrainValues</em>.</p>

<p>The type base::commands::Motion2D contains a demanded translation and a rotation speed. Translation is measured in m/s and
rotation in rad/s. The parameter deltaTime defines how much time advanced since the last call. The function
<em>computeNextPose</em> computes the new position and and rotation of our rock, in respect to the last pose.</p>

<pre><code class="language-cpp">base::samples::RigidBodyState RockControl::computeNextPose(
 const double &amp;deltaTime,
 const base::commands::Motion2D &amp;inputCommand)
{
 //limit the input values.
 base::commands::Motion2D command = inputCommand;
 constrainValues(command);

 //calculate displacement
 double delta_translation  = command.translation * deltaTime;
 double delta_rotation  = command.rotation * deltaTime;

 //apply new displacement to rock state
 currentHeading += delta_rotation;
 currentRoll += delta_translation * -2;

 //wrap angles into interval [-PI, PI]
 constrainAngle(currentHeading);
 constrainAngle(currentRoll);

 //calculate new absolute values for position and orientation
 currentPose.position +=
   Eigen::AngleAxisd(currentHeading, Eigen::Vector3d::UnitZ())
   * Eigen::Vector3d(0, delta_translation, 0);
 currentPose.orientation =
   Eigen::AngleAxisd(currentHeading, Eigen::Vector3d::UnitZ())
   * Eigen::AngleAxisd(currentRoll, Eigen::Vector3d::UnitX());
 currentPose.orientation.normalize();

 return currentPose;
}
</code></pre>

<p>Finally, you can build your library using <em>amake</em>.</p>

<h2 id="wrapping-it-up">Wrapping it up</h2>
<p>So, now that we are equipped with our library, we can go to the second step and wrap the code into an orocos task.
Therefore, we create a new orocos component. Again, we use the command &lsquo;rock-create-orogen&rsquo;. </p>

<pre><code class="language-text">cd ~/dev/tutorials/orogen/
rock-create-orogen rock_tutorial
</code></pre>

<h3 id="define-task">Define task</h3>

<p>Again we start by adding the build dependencies in the mainfest.xml. In this
case, we only depend on &lsquo;tutorials/rock_tutorial&rsquo;, as &lsquo;rock_tutorial&rsquo; already
depends on &lsquo;base/types&rsquo; and the dependencies are resolved recursively.</p>

<p>In <em>rock_tutorial.orogen</em>, we will define the orocos task. For this tutorial, we replace the existing entries by our definitions:</p>

<pre><code class="language-ruby">
name "rock_tutorial"
version "0.1"

import_types_from "rock_tutorialTypes.hpp"
import_types_from "base"
using_library "rock_tutorial"

task_context "RockTutorialControl" do
 # Declare input port motion_command
 input_port "motion_command", "base::commands::Motion2D"
 # Declare output port pose
 output_port "pose", "base::samples::RigidBodyState"
 # Set runtime behaviour
 periodic(0.01)
end
</code></pre>

<p>The task RockTutorialControl has an input port called &lsquo;motion_command&rsquo; of type base::commands::Motion2D and an output port called &lsquo;pose&rsquo;
of type base::samples::RigidBodyState. This task will compute a new position and orientation each time the update hook will triggered, given
the translation and rotation speed it receives on its input port. The latest motion command will be used if there is no new one.
The update hook of this task will be triggered periodically.</p>

<p>As with all <a href="110_basics_create_component.html">oroGen packages</a>, we call
&lsquo;rock-create-orogen&rsquo; to finalize the package creation, and can then write the
component implementation in <em>tasks/RockTutorialControl.hpp</em> and
<em>tasks/RockTutorialControl.cpp</em>. You will need the following attributes:</p>

<pre><code class="language-cpp">RockControl* control;
double taskPeriod;
</code></pre>

<p>These need to be declared in the header file and initialized in the
constructors of the source file.</p>

<p>The implementation of the <em>startHook</em> and the <em>updateHook</em> is simple, as the
task only calls the library and passes on the result.</p>

<pre><code class="language-cpp">bool RockTutorialControl::startHook()
{
 //delete last instance in case we got restarted
 if(control)
   delete control;

 //create instance of the controller
 control = new RockControl();

 //figure out the period in which the update hook get's called
 taskPeriod = TaskContext::getPeriod();

 return RockTutorialControlBase::startHook();
}

void RockTutorialControl::updateHook()
{
 //read new motion command if available
 base::commands::Motion2D motionCommand;
 _motion_command.readNewest(motionCommand);

 //compute new position based on the input command
 base::samples::RigidBodyState rbs =
   control-&gt;computeNextPose(taskPeriod, motionCommand);

 //set time stamp
 rbs.time = base::Time::now();

 //write pose on output port
 if(_pose.connected())
   _pose.write(rbs);
}
</code></pre>

<h2 id="run-it">Run it</h2>

<p>Now we should run it to see if it works. Create a <em>scripts</em> folder and create a
new <tt>rockTutorial1.rb</tt> script containing:</p>

<pre><code class="language-ruby">require 'orocos'
require 'readline'
include Orocos

## Initialize orocos ##
Orocos.initialize

## load and add the 3d plugin for the rock
Orocos.run 'rock_tutorial::RockTutorialControl' =&gt; 'rock_tutorial_control' do


 rockControl = Orocos.name_service.get 'rock_tutorial_control'

 ## Create a sample writer for a port ##
 sampleWriter = rockControl.motion_command.writer

 ## Start the tasks ##
 rockControl.start

 ## Write motion command sample ##
 sample = sampleWriter.new_sample
 sample.translation = 1
 sample.rotation = 0.5
 sampleWriter.write(sample)

 Readline::readline("Press Enter to exit\n") do
 end
end
</code></pre>

<p>and run it with</p>

<p class="commandline">ruby rockTutorial1.rb</p>

<p>As in the basics tutorial, the Ruby commands lead to a start of the task, i.e. calling the
<em>startHook()</em> of the task.
Afterwards we use a port writer to write a motion command to the &lsquo;motion_command&rsquo; port.</p>

<p>In another console start the task inspector with</p>

<p class="commandline">rock-display rock_tutorial_control</p>

<p>Right-klicking the output port <em>pose</em> gives you the option to use <em>RigidBodyStateVisualization</em>
to show the rock&rsquo;s state. It will be presented by coordinate axes showing position and orientation of
the rock.</p>

<p>If you want to steer the rock using a joystick, progress to <a href="510_joystick.html">the next
tutorial</a>. If you don&rsquo;t have a joystick,
go steer using a <a href="520_virtual_joystick.html">graphical interface</a>.</p>


</div>
</div>
</div>
