---
layout: page
title: Data Monitors
section: System Management
secondsection: Tutorials
---

<div class="content2">
<div class="content2-pagetitle">Data Monitors</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<div class="note">
<p>The result of this tutorial can be found in bundles/tutorials if you
followed the instructions at the bottom of <a href="../tutorials/index.html">this page</a>.
While the tip of the master branch contains the accumulated result of all the
tutorials, you can get the specific result of this one by checking out the
data_monitors tag with</p>

<pre><code>git checkout data_monitors
</code></pre>
</div>

<p>To build the state machine <a href="1100_action_state_machines.html">in the previous
tutorial</a>, we manually created <em>data monitoring
compositions</em>, whose job was to listen to certain data streams in the executed
network in order to find out when the state machine should transition.</p>

<p>If you look at the implementation of these two monitors, they are basically the
same:</p>

<ul>
<li>read a stream</li>
<li>emit an event when a condition is met</li>
</ul>

<p>This tutorial will show you how to define such monitors in a much more compact
and reusable way, and how these &lsquo;new&rsquo; monitors differ from the manually created
ones. We will then see in the next tutorial how lists of such monitors can be
defined in order to design fault detection and response tables</p>

<h2 id="defining-data-monitors">Defining Data Monitors</h2>

<p>Data monitors can be used in multiple places. We will see here how to integrate
them in an action state machine, in order to understand what they do. The
<a href="../system/data_monitoring_tables.html">reference documentation</a> lists all the
ways you can define tables and activate / deactivate them.</p>

<p>In action state machines, data monitors can be attached to states. One simply
calls #monitor on the state. Let&rsquo;s define a new state machine using this
feature:</p>

<pre><code class="language-ruby">describe('random motion within a delimited area').
optional_arg('fence_size',
  'size in meters of the fence around the origin',
  3).
optional_arg('threshold',
  'size in meters we need to be from the origin to consider that we have reached it',
  1)
action_state_machine 'fenced_random_move_with_monitors' do
# The 'move randomly' state
random = state random_def
# Monitor that verifies that we don't go outside the fence. Emit the
# cross_fenced event on the state machine's task when it happens
#
# The hash at the end passes options from the state machine
# to the monitor. They are available as local variables
# inside
random.monitor(
    'fence', # monitor name
    random.rock_child.pose_samples_port, # data sources
    :fence_size =&gt; fence_size). # arguments
  trigger_on do |pose|
    pos = pose.position
    pos.x.abs &gt; fence_size || pos.y.abs &gt; fence_size
  end.
  # emit this event when the predicate is true
  emit crossed_fence_event

# The move-to-origin state
origin = state to_origin_def.use(rock1_dev)
# Monitor that waits for us to be close enough to the center
origin.monitor(
    'reached_center',
    origin.rock_child.pose_samples_port,
    :threshold =&gt; threshold).
  trigger_on do |pose|
    pos = pose.position
    pos.x.abs &lt; threshold &amp;&amp; pos.y.abs &lt; threshold
  end.
  # emit this event when the predicate is true
  emit origin.success_event

# We start by moving randomly
start random
# We transition each time the rock passes the fence
transition random, crossed_fence_event, origin
# And transition back when we reach the origin
transition origin, origin.success_event, random
end
</code></pre>

<p>The only bit missing is the definition of the crossed_fence event. For the
origin monitor, we reuse the success event of the origin state, but for the
fence we have to define a new one.</p>

<p>We therefore have to create a new Roby task model that is going to be
representing the action state machine, and add this event to it. When one does
not create such a model explictly, Roby creates a new one that is named after
the state machine&rsquo;s name, i.e. in this case FencedRandomMoveWithMonitors.</p>

<p>Let&rsquo;s now define one explicitly and then tell Roby
that it should be used as the root model for the state machine.</p>

<p>in models/tasks/fenced_random_move.rb</p>

<pre><code class="language-ruby">module Tutorials
class FencedRandomMove &lt; Roby::Task
  terminates # Always add this unless you know what you are doing
  event :crossed_fence
end
end
</code></pre>

<p>and in the action file:</p>

<pre><code class="language-ruby">describe('random motion within a delimited area').
returns(Tutorials::FencedRandomMove).
optional_arg('fence_size',
  'size in meters of the fence around the origin',
  3).
optional_arg('threshold',
  'size in meters we need to be from the origin to consider that we have reached it',
  1)
</code></pre>

<p>The resulting behaviour can be started and tested with</p>

<pre><code>syskit run -rtut-transformer fenced_random_move_with_monitors!
</code></pre>

<h2 id="summary">Summary</h2>

<p>The data monitors as used in the state machines are compact ways to make the
state machines transition based on information in the data. They allow to easily
transform data triggers into Roby events.</p>

<p>The <a href="1300_fault_response_tables.html">next tutorial</a> will show you how they can
also be used in conjunction with fault response tables to define fault detection
/ fault response systems</p>


</div>
</div>
</div>
