---
layout: page
title: Refining models - a leader/follower system
section: System Management
secondsection: Tutorials
---
<div class="content2">
<div class="content2-pagetitle">Refining models - a leader/follower system</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p class="note">The result of this tutorial can be found in bundles/tutorials_scripts if you
followed the instructions at the bottom of <a href="../tutorials/index.html">this page</a></p>

<h2 id="abstract">Abstract</h2>
<p>In this tutorial, you will learn one of the most important feature for
model-based systems: how generic models can be refined. In object-oriented
programming, this is more or less equivalent to subclassing.</p>

<p>This tutorial will guide through the process of:</p>

<ul>
 <li>changing the RockControl composition so that it can be adapted to the
requirements of more complex motion control generators</li>
 <li>how to transform a composition into a service, i.e. how to add ports to a
composition</li>
</ul>

<h2 id="the-leader-follower-system">The leader-follower system</h2>
<p>This tutorial will deploy a network in which:</p>

<ul>
 <li>one rock, controlled by the random motion generator, is a leader</li>
 <li>another rock is following the first rock</li>
</ul>

<p>The motion generator component for the follower is tut_follower::Task, included
in <a href="../tutorials/index.html#installing">Rock&rsquo;s rock.tutorial package set</a>. It
requires an additional sensor component, tut_sensor::Task, that &lsquo;senses&rsquo; the
bearing and distance to the target.</p>

<p>The goal will be to reuse the same base model &lsquo;Tutorials::RockControl&rsquo; for both
deployments. The idea behind reusing in this case is:</p>

<ul>
 <li>you might have a diagnostics routine that is able to monitor the movement of
a rock regardless of what controls it. Attaching that diagnostics routine to
the RockControl composition will be sufficient, as it will apply to all
refinements of this model.</li>
 <li>it simplifies the usage of the compositions, as one only has to remember the
name of a composition (Tutorials::RockControl) and give an intent (&lsquo;run the composition
with X or Y&rsquo;) instead of having to remember N composition names.</li>
</ul>

<h2 id="reusing-models">Reusing Models</h2>
<p>The first thing that we will learn in this page is how to reuse common models.
This is critical: one does not want to recreate every single data service or
composition each time a new robot needs to be integrated. Moreover, it gives
functional definition of <em>generic</em> complex behaviours, i.e. &ldquo;how to run this
particular SLAM on a four-wheeled skid-steered robot&rdquo; can very well become a
(reusable / refinable) definition.</p>

<p>One bundle on which every other bundle depends by default is the Rock bundle.
For simplicity reasons, we removed this dependency <a href="100_moving_to_bundles.html">in the starting
tutorial</a>. Let&rsquo;s undo this now, and check that we
indeed are using the rock bundle using bundle-info:</p>

<pre><code># mv config/bundle-yml.bak config/bundle.yml
# bundle-info
Bundles[INFO]: Active bundles: tutorial, rock
</code></pre>

<p class="warning">This is going to break the joystick ! The reason is contained in the
explanations <a href="../system/devices.html">on devices</a>.</p>

<p>For our purposes here, we will want to have a data service that generates a pose
(in particular, the poses of both our leader and follower). Let&rsquo;s have a look at
what is available. Open the Syskit model browser</p>

<pre><code># syskit browse
</code></pre>

<p>And look into the &lsquo;Base&rsquo; entry. You will find there a Base::PoseSrv service that
fits:</p>

<p><a href="600_syskit_browse_pose_service.png"><img src="600_syskit_browse_pose_service_thumb.png" alt="Sykit browse" /></a></p>

<p>At the top of the service&rsquo;s description, note the</p>

<pre><code class="language-ruby">require 'rock/models/blueprints/pose'
</code></pre>

<p>This is the line that needs to be used to make this particular data service
available (when required).</p>

<h2 id="specializations">Specializations</h2>
<p>In Syskit, refinements of a composition model can be
created with <em>specializations</em>. Specializations can be seen as conditional
application of changes on compositions.</p>

<p>For instance, the following specialization specifies that when using
Tutorials::RockControl, in cases where the command generator is a TutFollower::Task,
one child and two connections must be added to the composition. Copy
scripts/03_defines.rb to scripts/04_leader_follower.rb, declare the TutFollower::Task to
provide the CommandGeneratorSrv service and add the code for the
specialization after the declaration of the Tutorials::RockControl composition.</p>

<pre><code class="language-ruby"># Load common data services from the Rock bundle
require 'rock/models/blueprints/pose'
using_task_library 'tut_follower'
using_task_library 'tut_sensor'
TutFollower::Task.provides Tutorials::CommandGeneratorSrv, :as =&gt; 'cmd'
...

# This is the syntax to add specializations to existing composition models (i.e.
# after the class ... end definition)
Tutorials::RockControl.specialize Tutorials::RockControl.cmd_child =&gt; TutFollower::Task do
 # It will need some other task/composition to provide the target pose
 add Base::PoseSrv, :as =&gt; "target_pose"
 # And the sensor processing to compute the bearing/distance to target
 add TutSensor::Task, :as =&gt; 'sensor'

 # We must specify the port on the sensor child, since
 # there are two matches
 #
 # The rock child is already defined in the 'base' Tutorials::RockControl
 # composition
 target_pose_child.connect_to sensor_child.target_frame_port
 rock_child.connect_to sensor_child.local_frame_port
 sensor_child.connect_to cmd_child
end
</code></pre>

<p>We have to make the TutFollower and the TutSensor tasks&rsquo; oroGen projects available, and thus added the following lines to the includes at the top of the file:</p>

<pre><code class="language-ruby">using_task_library 'tut_follower'
using_task_library 'tut_sensor'
</code></pre>

<p>Once the specialization hase been added, it allows you to use the same composition for the random controller
&lsquo;TutBrownian::Task&rsquo; and the follower controller &lsquo;TutFollower::Task&rsquo;, without
having to care about the details (or at least, not yet)</p>

<pre><code class="language-ruby"># Selects the "standard" composition
define 'random', Tutorials::RockControl.use(TutBrownian::Task)
# Selects the "specialized" composition
define 'follower', Tutorials::RockControl.use(TutFollower::Task)
</code></pre>

<p>However, for the second one to be valid, we need a provider for the
Base::PoseSrv service: our &lsquo;leader&rsquo; composition- which is a Tutorials::RockControl
composition itself. For that to be possible, we need the &lsquo;leader&rsquo; composition
to provide the Base::PoseSrv service.</p>

<p>We therefore need:</p>

<ul>
 <li>the composition to have an output port of type base/samples/RigidBodyState</li>
 <li>a declaration of the fact that the composition does provide the data service</li>
</ul>

<h2 id="adding-ports-to-compositions">Adding ports to compositions</h2>
<p>Compositions cannot have their own ports. They can only <em>export</em>
ports from their children. In our case, the rock&rsquo;s pose is provided by the
RockTutorialControl task&rsquo;s pose_samples port. We will now change the original composition
model so that it exports this port and we declare the composition as
providing a Base::PoseSrv</p>

<pre><code class="language-ruby">module Tutorials
 class RockControl &lt; Syskit::Composition
   ...
   export rock_child.pose_samples_port
   provides Base::PoseSrv, :as =&gt; 'pose'
 end
end
</code></pre>

<p>It is now possible to define our leader-follower system:</p>

<pre><code class="language-ruby">define 'leader',
 Tutorials::RockControl.use(TutBrownian::Task)
define 'follower',
 Tutorials::RockControl.use(TutFollower::Task, leader_def)
</code></pre>

<p>Try out the current definition:</p>

<pre><code># syskit instanciate scripts/04_leader_follower.rb follower_def!
= cannot deploy the following tasks
TutSensor::Task:0x4a92050{conf =&gt; [default]}[]
 child sensor of Tutorials::RockControl/[cmd.is_a?(TutFollower::Task)]:0x5bfa298{conf =&gt; [default]}[]
TutFollower::Task:0x5c05030{conf =&gt; [default]}[]
 child cmd of Tutorials::RockControl/[cmd.is_a?(TutFollower::Task)]:0x5bfa298{conf =&gt; [default]}[]
RockTutorial::RockTutorialControl:0x5d36d28{conf =&gt; [default]}[]
 child rock of Tutorials::RockControl/[cmd.is_a?(TutFollower::Task)]:0x5bfa298{conf =&gt; [default]}[]
TutSensor::Task:0x4a92050{conf =&gt; [default]}[]: no deployments available
TutFollower::Task:0x5c05030{conf =&gt; [default]}[]: no deployments available
RockTutorial::RockTutorialControl:0x5d36d28{conf =&gt; [default]}[]: some deployments exist, but they are already used in this network
 task rock_tutorial from deployment Deployments::RockTutorial on localhost
   already used by RockTutorial::RockTutorialControl:0x5df4f08{orocos_name =&gt; rock_tutorial, conf =&gt; [default]}[]: child rock of Tutorials::RockControl:0x5c8f0a0{conf =&gt; [default]}[]
</code></pre>

<p>Ah &hellip; yes, we need some extra deployments. The tut_deployment project contains all we
need so let&rsquo;s simply replace all the use_deployment lines:</p>

<pre><code class="language-ruby">Syskit.conf.use_deployment 'joystick'
Syskit.conf.use_deployment 'rock_tutorial'
Syskit.conf.use_deployment 'brownian'
</code></pre>

<p>by</p>

<pre><code class="language-ruby">Syskit.conf.use_deployments_from 'tut_deployment'
</code></pre>

<p>and re-run syskit instanciate.</p>

<pre><code>cannot deploy the following tasks
RockTutorial::RockTutorialControl:0x50dcb90{conf =&gt; [default]}[]
 child rock of Tutorials::RockControl:0x3c00028{conf =&gt; [default]}[]
RockTutorial::RockTutorialControl:0x515dad8{conf =&gt; [default]}[]
 child rock of Tutorials::RockControl/[cmd.is_a?(TutFollower::Task)]:0x50128e0{conf =&gt; [default]}[]
RockTutorial::RockTutorialControl:0x50dcb90{conf =&gt; [default]}[]: multiple possible deployments, choose one with #use_deployments
 task rock_tutorial from deployment rock_tutorial defined in tut_deployment on localhost
 task target from deployment target defined in tut_deployment on localhost
 task follower from deployment follower defined in tut_deployment on localhost
RockTutorial::RockTutorialControl:0x515dad8{conf =&gt; [default]}[]: multiple possible deployments, choose one with #use_deployments
 task rock_tutorial from deployment rock_tutorial defined in tut_deployment on localhost
 task target from deployment target defined in tut_deployment on localhost
 task follower from deployment follower defined in tut_deployment on localhost
</code></pre>

<p>This time, the issue is different: there are more than one deployment available
for a particular task, and we therefore need to tell Syskit which one should be
used. This is done by providing a regular expression that matches the
deployment names that are <em>preferred</em> for a particular subsystem. Let&rsquo;s do this:</p>

<pre><code class="language-ruby">define 'joystick',    Tutorials::RockControl.
   use(Controldev::JoystickTask)
define 'random',      Tutorials::RockControl.
   use(TutBrownian::Task)
define 'random_slow', Tutorials::RockControl.
   use(TutBrownian::Task.with_conf('default', 'slow'))
define 'random_slow2', Tutorials::RockControl.
   use(TutBrownian::Task).with_conf('slow')
define 'leader', Tutorials::RockControl.
   use(TutBrownian::Task).
   use_deployments(/target/)
define 'follower', Tutorials::RockControl.
   use(TutFollower::Task, leader_def).
   use_deployments(/follower/)
</code></pre>

<p>Finally you can run and display the rocks using rock-display:</p>

<pre><code># syskit run scripts/04_leader_follower.rb follower_def!
</code></pre>

<h2 id="summary">Summary</h2>
<p>In this tutorial, you:</p>

<ul>
 <li>learned how to use specializations to refine the compositon models</li>
 <li>learned how to &ldquo;create&rdquo; ports on a composition</li>
</ul>

<p>What&rsquo;s left is to describe how all the bits and pieces are organized within a
bundle folder structure. This is the subject of the <a href="700_file_structure.html">next and
last</a> tutorial.</p>



</div>
</div>
</div>
