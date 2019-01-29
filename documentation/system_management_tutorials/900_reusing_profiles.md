---
layout: page
title: Introduction
section: System Management
secondsection: Tutorials
---

<div class="content2">
<div class="content2-pagetitle">Reusing Profiles</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<div class="note">
<p>The result of this tutorial can be found in bundles/tutorials if you
followed the instructions at the bottom of <a href="../tutorials/index.html">this page</a>.
While the tip of the master branch contains the accumulated result of all the
tutorials, you can get the specific result of this one by checking out the
reusing-profiles tag with</p>

<pre><code>git checkout reusing-profiles
</code></pre>
</div>

<p>While we have already seen <a href="700_file_structure.html#reusing-other-bundles">how to reuse other
bundles</a>, we have yet to see how
to reuse profiles. Indeed, one of the major ideas behind the profile concept is that one can design
subsystems that get more and more specialized as their target platform is more
and more precise. For instance, bundles/rock_ugv_nav, the bundle that contains
models for doing rover navigation in Rock, defines a main navigation profile for
all rovers and then specializes it for the 4-wheel skid-steered class of
vehicle, vehicles for which Rock has standard components to handle control and
odometry.</p>

<p>In <a href="1000_transformer.html">the next tutorial</a>, we will want to be able to modify
the current definitions so that another component is used in place of the
tut_sensor::Task component. This tutorial will guide you through the process of
reusing profiles, i.e. create one common profile with the bulk of the
definitions and then reuse it in one where tut_sensor::Task is used and another
one that will be ready to be filled in the next tutorial. This is also commonly
how the split between real and simulated systems is done: a main profile does
the common definitions, that then get specialized in the real-robot and in the
simulated-robot cases.</p>

<p>We will, in this tutorial, split the
Tutorials::Rocks bundle into one profile that handles the current way (without
the transformer) and one that uses the transformer-enabled component. </p>

<p>Two main concepts are going to be used for this:</p>

<ul>
<li>subclassing (in the Ruby-as-a-programming-language sense of the term)
compositions to create more specific versions of them,</li>
<li>separating files into robot categories in a bundle,</li>
<li>reusing profiles to refine them</li>
</ul>

<h2 id="abstracting-the-sensor-component">Abstracting the sensor component</h2>
<p>The first modification will be to make Tutorials::RockControl independent of the
sensor component used. This is of course done by introducing a new data service
and using it there instead.</p>

<p>Let&rsquo;s define first the service in models/blueprints/distance_bearing_sensor_srv.rb</p>

<pre><code class="language-ruby">import_types_from 'tut_sensor'
module Tutorials
data_service_type 'DistanceBearingSensorSrv' do
  output_port 'samples', '/rock_tutorial/BearingDistanceSensor'
end
end
</code></pre>

<p>And declare in models/orogen/tut_sensor.rb that Task provides it</p>

<pre><code class="language-ruby">require 'models/blueprints/distance_bearing_sensor_srv'
TutSensor::Task.provides Tutorials::DistanceBearingSensorSrv,
:as =&gt; 'sensor'
</code></pre>

<p>Now, if we have a look at the follower specialization for RockControl (in
models/blueprints/rock_control.rb)</p>

<pre><code class="language-ruby">specialize cmd_child =&gt; TutFollower::Task do
add Base::PoseSrv, :as =&gt; "target_pose"
add TutSensor::Task, :as =&gt; 'sensor'

target_pose_child.connect_to sensor_child.target_frame_port
rock_child.connect_to sensor_child.local_frame_port
sensor_child.connect_to cmd_child
end
</code></pre>

<p>we see that a lot of it is actually specific for tut_sensor::Task. If we wanted
to stick with specializations, we would create a first specialization for
DistanceBearingSensorSrv, and then specialize it for TutSensor::Task. However,
this kind of recursive specializations is not supported in Syskit, so we&rsquo;ll have
to deal with it differently.</p>

<p>Instead, we will create a RockFollower composition that is a <strong>subclass</strong> of
RockControl, and specialize it for the different kinds of sensors. Remove the
specialize block in RockControl and then add:</p>

<pre><code class="language-ruby"># Submodel of RockControl for follower behaviours
class RockFollower &lt; RockControl
overload cmd_child, TutFollower::Task
add DistanceBearingSensorSrv, :as =&gt; 'sensor'
sensor_child.connect_to cmd_child

specialize sensor_child =&gt; TutSensor::Task do
  add Base::PoseSrv, :as =&gt; 'target_pose'
  target_pose_child.connect_to sensor_child.target_frame_port
  rock_child.connect_to sensor_child.local_frame_port
end
end
</code></pre>

<p>The existing profile in models/profiles/rocks.rb will have to be changed by
replacing RockControl with RockFollower for the follower definition.</p>

<p>Let&rsquo;s make the result fail with</p>

<pre><code># syskit instanciate -rtut follower_def!
`block in use': target_pose is not a known child of Tutorials::RockFollower (RuntimeError)
</code></pre>

<p>The issue this time is the following:</p>

<ul>
<li>we give a selection for &lsquo;target_pose&rsquo;, which is meant to be a child of
RockFollower</li>
<li>the target_pose child is only defined in the specialization of RockFollower</li>
<li>nothing in the use() specification allows Syskit to determine that it should
specialize.</li>
</ul>

<p>The solution is therefore to give the sensor component explicitly, so that the
specialization is indeed selected:</p>

<pre><code class="language-ruby">define 'follower', Tutorials::RockFollower.
use(TutFollower::Task, TutSensor::Task, rock2_dev, 'target_pose' =&gt; leader_def)
</code></pre>

<h2 id="reusing-profiles">Reusing Profiles</h2>
<p>We will, in the next tutorial, also want to use TutSensor::TransformerTask as a
possible sensor. Let&rsquo;s make this possible right now.</p>

<p>In models/orogen/tut_sensor.rb, add</p>

<pre><code class="language-ruby">TutSensor::TransformerTask.provides Tutorials::DistanceBearingSensorSrv,
  :as =&gt; 'sensor'
</code></pre>

<p>There are now two possible components for DistanceBearingSensorSrv. We
could simply create a new definition for TutSensor::TransformerTask. However,
we will use this situation to explain how to reuse profiles instead.</p>

<p>What we are going to do now is create a base profile that is common between the
non-transformer and the transformer-enabled cases. Then, we will have the
RocksWithoutTransformer profile on the one hand, and the RocksWithTransformer on
the other hand. Finally, we will create two robot configurations: one that uses
the transformer and one that does not.</p>

<p><strong>First part</strong>: creating the Base profile. This is trivially done by renaming the
Rocks profile into BaseRocks, and modify the follower definition to be
independent of the sensor type.</p>

<pre><code class="language-ruby">profile 'BaseRocks' do
...
define 'follower', Tutorials::RockFollower.
  use(TutFollower::Task, rock2_dev)
end
</code></pre>

<p><strong>Second part</strong>: making the two profiles, one that is identical to the one we
had so far, and one for the transformer case. To achieve this, we create two new
Rocks profile and tell Syskit that they <strong>use</strong> the base one. Finally we add
some more dependency selection that is then <strong>limited</strong> to these new profiles.
At the end, we refine the follower definition in the no-transformer case to
match what we had beforehand.</p>

<pre><code class="language-ruby">profile 'RocksWithoutTransformer' do
use_profile BaseRocks
define 'follower', follower_def.use(TutSensor::Task, 'target_pose' =&gt; leader_def)
end
profile 'RocksWithTransformer' do
use_profile BaseRocks
define 'follower', follower_def.use(TutSensor::TransformerTask)
end
</code></pre>

<p><strong>Finally</strong> create the two robot configurations. We now need to have a way to
select which profiles to apply from the command line. This is done through
Roby&rsquo;s robot configuration mechanism.</p>

<p>We&rsquo;ll keep the &lsquo;tut&rsquo; robot as our non-transformer robot. Let&rsquo;s now create a new
tut-transformer robot for the other case:</p>

<pre><code>roby add-robot tut-transformer
</code></pre>

<p>What we did so far is use the Rocks profile in models/actions/main.rb. Since
this is the action definition for all the robots, we need to move the
use_profile statement into models/actions/tut/main.rb (don&rsquo;t forget to rename Rocks into
RocksWithoutTransformer), which is specific to the tut
robot. And do the corresponding modification in
models/actions/tut-transformer/main.rb</p>

<pre><code class="language-ruby">require 'models/profiles/rocks'
class Main &lt; Roby::Actions::Interface
use_profile Tutorials::RocksWithTransformer
end
</code></pre>

<p>We also should not forget to add the use_deployments line in
config/tut-transformer.rb (it is at the top of config/tut.rb). The transformer
case is now available by selecting the tut-transformer robot:</p>

<pre><code>syskit instanciate -rtut-transformer follower_def!
</code></pre>

<h2 id="conclusion">Conclusion</h2>
<p>The profile-reusing mechanism creates scopes for definitions and selections of
tasks for services. This scoping mechanism applies to all the profile elements,
such as <a href="800_devices.html">robot definition blocks</a> or transformer
configuration. In other words: if it is defined in a profile, it is scoped to
this particular profile.</p>

<p>We can now finally move to the <a href="1000_transformer.html">transformer tutorial</a></p>



</div>
</div>
</div>
