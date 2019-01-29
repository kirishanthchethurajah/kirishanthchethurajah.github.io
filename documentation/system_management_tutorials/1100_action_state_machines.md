---
layout: page
title: Action State Machines
section: System Management
secondsection: Tutorials
---

<div class="content2">
<div class="content2-pagetitle">Action State Machines</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<div class="note">
 <p>The result of this tutorial can be found in bundles/tutorials if you
followed the instructions at the bottom of <a href="../tutorials/index.html">this page</a>.
While the tip of the master branch contains the accumulated result of all the
tutorials, you can get the specific result of this one by checking out the
action_state_machine tag with</p>

 <pre><code>git checkout action_state_machine
</code></pre>
</div>

<p>So far, we have seen how to define and compose network of components. The rest
of the tutorials will guide through the process of <em>coordinating</em> them, i.e. how
to make the system switch between them when some conditions are reached, thus
creating temporal combinations of behaviours.</p>

<p>This tutorial will guide you through:</p>

<ul>
 <li>how to combine the network configurations created in profiles into more
complex behaviours by using state machines</li>
 <li>how these state machines can then be combined with each other</li>
</ul>

<p>The action state machines presented in this tutorial are not specific to Syskit,
but are available to any Roby action. You may want to also read the <a href="/api/tools/roby">Roby
documentation</a> for more information about them. However, Syskit
does extend these state machines to give some Syskit-specific functionality.</p>

<h2 id="defining-state-machines">Defining State Machines</h2>
<p>In the previous tutorials, we have mainly built two behaviours for our rocks:</p>

<ul>
 <li>one random movement behaviour</li>
 <li>one target-following behaviour</li>
</ul>

<p>Let&rsquo;s now assume that we want a rock to move randomly, but stay confined in a
certain area around the origin. This can be achieved with what we currently
have:</p>

<ul>
 <li>if within the area, move randomly</li>
 <li>if on the border, go back to origin</li>
</ul>

<p>The &lsquo;go back to origin&rsquo; behaviour being obviously &lsquo;follow the origin frame&rsquo;.
Let&rsquo;s define it first generically:</p>

<pre><code class="language-ruby">define 'to_origin', Tutorials::RockFollower.
 use(TutFollower::Task, TutSensor::TransformerTask).
 use_frames('target' =&gt; 'world', 'world' =&gt; 'world')
</code></pre>

<p>Such a switch between behaviours can classically be represented by a state
machine. This is one mean to express such combination of behaviours in Syskit as
well. We will see how to write such a state machine now.</p>

<p>Action state machines are defined on action interfaces. We have already seen
those as the place for the use_profile statements. Let&rsquo;s now add our state
machine to it (in our case models/actions/tut-transformer/main.rb).</p>

<pre><code class="language-ruby">class Main &lt; Actions::Interface
 use_profile Tutorials::RockWithTransformer

 describe 'move randomly as long as in a 10 meter square \
   around origin, move back to origin when passing the border'
 action_state_machine 'fenced_random_move' do
   # We need to call #state to transform an action / definition
   # into a proper state
   random = state random_def
   origin = state to_origin_def.use(rock1_dev)

   # We start by moving randomly
   start random
   # We transition each time the rock passes the fence
   transition ???, origin
   # And transition back when we reach the origin
   transition ???, random
 end
end
</code></pre>

<p>Notice above that there are two &ldquo;???&rdquo; placeholders in the two transition
statements. We have to specify transitions, but how ?</p>

<p>Since we are under Roby, the transitions are obviously going to be caused by
Roby events. However, there is still the issue of defining them.</p>

<p>One constraint is that we should not modify the behaviours to be modified to
account for this very particular combination of them (we want them to be
reusable, therefore free of this very specific use case), we&rsquo;ll define two
monitoring networks. One will check whether the rock passes the fence and the
other whether it is within its target pose.</p>

<p>Let&rsquo;s create the fence monitor first in models/blueprints/fence_monitor.rb</p>

<pre><code class="language-ruby">require 'rock/models/blueprints/pose'
module Tutorials
 # Given a position provider, checks whether this position crosses a specified
 # boundary around the origin
 class FenceMonitor &lt; Syskit::Composition
   # The size of the allowed square
   argument :fence_size, :default =&gt; 10
   # Emitted when the tracked pose passes the required fence towards the
   # outside
   event :passed_fence_outwards

   # The position provider that we will be monitoring
   add Base::PositionSrv, :as =&gt; 'position'

   # Tests if the given position is within the fence
   #
   # @param [Types::Base::Position] the position
   # @return [Boolean]
   def in_fence?(p)
     p.x.abs &lt; fence_size &amp;&amp; p.y.abs &lt; fence_size
   end

   # This script context allows us to have a proper access to ports in
   # children. A raw Roby poll block would be possible, but would require that
   # we know the type of the final position component.
   script do
     # Get a reader object that allows us to read the position
     position_r = position_child.position_samples_port.reader
     # The block given to #poll is executed once per Roby execution cycle
     # (usually once per 100ms)
     poll do
       # If we have a position
       if p = position_r.read_new
         # p is a RigidBodyState, we only need the position part of it
         p = p.position
         # Initialize @last_p if it is not initialized yet
         @last_p ||= p
         # If we cross the border, emit the event
         if in_fence?(@last_p) &amp;&amp; !in_fence?(p)
           passed_fence_outwards_event.emit
         end
         @last_p = p
       end
     end
   end
 end
end
</code></pre>

<p>Then, the target reached monitor in models/blueprints/position_within_threshold_monitor.rb</p>

<pre><code class="language-ruby">require 'rock/models/blueprints/pose'
module Tutorials
 # Composition that checks whether a position is within a certain threshold
 # from a target
 class PositionWithinThresholdMonitor &lt; Syskit::Composition
   # The target pose, as Types::Base::Position
   argument :target
   # The target threshold
   argument :threshold, :default =&gt; 1
   # Emitted when the tracked pose is within threshold of the target
   event :reached

   # The position provider we are monitoring
   add Base::PositionSrv, :as =&gt; 'position'

   # Tests if the given position is within threshold of the target
   #
   # @param [Types::Base::Position] the position
   # @return [Boolean]
   def in_threshold?(p)
     (p - target).norm &lt; threshold
   end

   # A script context. This allows us to have a proper access to ports in
   # children. A raw Roby poll block would be possible, but would require that
   # we know the type of the final position component.
   script do
     # Get a data reader on the position provider
     position_r = position_child.position_samples_port.reader
     # The block given to #poll is executed once per Roby execution cycle
     # (usually once per 100ms)
     poll do
       # Emit if we have a position and it is within threshold
       if (p = position_r.read_new) &amp;&amp; in_threshold?(p.position)
         reached_event.emit
       end
     end
   end
 end
end
</code></pre>

<p>Let&rsquo;s now integrate the monitors in our state machine:</p>

<pre><code class="language-ruby">describe('move randomly as long as in a 10 meter square around origin, move back
to origin when passing the border')
action_state_machine 'fenced_random_move' do
 # We need to call #state to transform an action / definition into a proper
 # state
 random = state random_def
 # 'task' is used for tasks that are not used as states
 fence_monitor = task Tutorials::FenceMonitor.use(rock1_dev)
 # We need this monitor only in the random state
 random.depends_on fence_monitor

 origin = state to_origin_def.use(rock1_dev)
 # 'task' is used for tasks that are not used as states
 target_monitor = task Tutorials::PositionWithinThresholdMonitor.
   use(rock1_dev).with_arguments('target' =&gt; Types::Base::Position.new(0, 0, 0))
 # We need this monitor only in the origin state
 origin.depends_on target_monitor

 # We start by moving randomly
 start random
 # We transition each time the rock passes the fence
 transition random, fence_monitor.passed_fence_outwards_event, origin
 # And transition back when we reach the origin
 transition origin, target_monitor.reached_event, random
end
</code></pre>

<h2 id="runtime-behaviour">Runtime Behaviour</h2>

<p>You can now start the state machine with</p>

<pre><code>syskit run -rtut-transformer fenced_random_move!
</code></pre>

<p>and visualize the resulting behaviour using roby-display (for the Syskit part)
and rock-display (for the components)</p>

<h2 id="combining-state-machines">Combining State Machines</h2>
<p>When one creates a state machine, it becomes an action. It can therefore be used
in another state machine (and so on, and so forth), as for instance:</p>

<pre><code class="language-ruby">describe "combining state machines"
action_state_machine 'main' do
 movement = state fenced_random_move
 # Use a hypothetical go_and_recharge action which can be any Roby action
 # (Syskit network, state machine, ...)
 recharge = state go_and_recharge
 # And monitor the battery using a Syskit definition
 battery_monitor = state battery_monitor_def

 start movement
 movement.depends_on battery_monitor
 transition movement, battery_monitor.battery_low_event, recharge
 transition recharge, recharge.success_event, movement
end
</code></pre>

<h2 id="summary">Summary</h2>

<p>This tutorial has shown you how:</p>

<ul>
 <li>combine the networks defined in the Syskit profiles as states in a state
machine, thus allowing to combine them in a temporal way</li>
 <li>define separate monitoring networks, and use them to trigger the transition</li>
 <li>how state machines can then become part of bigger state machines</li>
</ul>

<p>These action state machines are great to define the nominal operations of a
robot, i.e. its mission (or parts of it). However, they would become very
cumbersome to handle errors, or thing that can happen from any state. One cannot
add transitions from any state to the error handlers.</p>

<p>The next tutorial will deal with a refinement of the monitors we have manually
defined here, introducing data monitors that allow to generate errors when
patterns are found in the data. Then, we will see how to handle these errors in
a graceful way.</p>



</div>
</div>
</div>
