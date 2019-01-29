---
layout: page
title: Profiles
section: System Management
secondsection: system
---
<div class="content2">
<div class="content2-pagetitle">Profiles</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="profiles">Profiles</h2>
<p>At some point, one has to bind everything together. We have so far a bunch of
models, and a way to specify our actual needs. What we are now looking for is a
way to provide a consistent set of predefined component networks that will be
the actual robot function.</p>

<p>This consistent set of definitions is called a profile. Profiles are defined
inside a module with:</p>

<pre><code class="language-ruby">module Profiles
profile "ProfileName" do
end
end
</code></pre>

<p>This creates a ProfileName object in the enclosing module.</p>

<div class="block">
<p><strong>Generation</strong> Template for a profile, following Syskit&rsquo;s naming and filesystem
conventions, can be created with</p>

<pre><code>syskit gen profile name/of_profile
</code></pre>

<p>for instance</p>

<pre><code>syskit gen profile rovers/localization
</code></pre>

<p>would create a Rock::Profiles::Rovers::Localization profile in
models/profiles/rovers/localization.rb, and the associated test file
test/profiles/rovers/test_localization.rb. Files in the hierarchy
(models/profiles/rovers.rb and test/profiles/suite_rovers.rb) are updated to
require the newly created files.</p>
</div>

<p><strong>Naming</strong> Profiles are by convention defined under the BundleName::Profiles
namespace (e.g. Rock::Profiles for the rock bundle). </p>

<p class="block"><strong>Filesystem</strong> Profiles are defined in models/profiles/. The file
names should be the snake_case version of the profile name (e.g.
models/profiles/my_project.rb for the profile defined above). Any task context,
composition or data service used in the profile file should be required first
with either the using_task_library statement (for task contexts) or a simple
require of the relevant file. Use <a href="general_concept.html#browsing">syskit browse</a>
to find out what is defined where.</p>

<p>The first thing one can do in a profile is to give names to <a href="subsystem_design.html">instance
requirements</a>. For instance, following the example above, one would probably
create a pose estimation definition:</p>

<pre><code class="language-ruby">profile "Base" do
define "pose_estimation", Compositions::PoseEstimation.
  use('orientation' =&gt; OroGen::XsensImu::Task.with_conf('default', 'high_sampling_rate'))
end
</code></pre>

<p>Once this definition is created, it can be referred to by using the _def suffix.
For instance, one would add the corridor servoing definition from the previous
examples with:</p>

<pre><code class="language-ruby">profile "Base" do
define "pose_estimation", Compositions::PoseEstimation.
  use('orientation' =&gt; OroGen::XsensImu::Task.with_conf('default', 'high_sampling_rate'))
define "servoing", Compositions::CorridorNavigation::CorridorServoing.
  use('pose' =&gt; pose_estimation_def)
end
</code></pre>

<h2 id="profile-wide-selections">Profile-wide selections</h2>
<p>The &ldquo;use&rdquo; statement allows to provide selections at the level of a profile, e.g.
for all the definitions in that profile. Each definition can, of course,
override that profile-wide setting. For instance:</p>

<pre><code class="language-ruby">profile "Base" do
define "pose_estimation", Compositions::PoseEstimation.
  use('orientation' =&gt; OroGen::XsensImu::Task.with_conf('default', 'high_sampling_rate'))
use Services::Pose =&gt; pose_estimation_def
end
</code></pre>

<p>A common usage for this statement is to give some default configuration for a
class of type contexts. For instance:</p>

<pre><code class="language-ruby">profile "Base" do
use OroGen::PoseEstimator::Task =&gt;
  OroGen::PoseEstimator::Task.use_conf('default', 'low_latency')
end
</code></pre>

<h2 id="reusing-profiles">Reusing Profiles</h2>
<p>Profiles are often an extension of existing &ldquo;generic&rdquo; profiles. For instance,
Rock&rsquo;s rock_ugv_nav bundle defines the RockUGVNav::Profiles::Skid4 profile that gives
common definitions for any system that is a four-wheeled, skid-steered system.
This is a great way to provide pre-integrated functionality for some classes of
systems, leaving only the &ldquo;binding to the specific platform&rdquo; to the system&rsquo;s
integrator.</p>

<p>Reusing such a profile is done in two steps. First, one must require the file of
the parent profile</p>

<pre><code>require 'rock_ugv_nav/models/profiles/skid4.rb'
</code></pre>

<p>Second, one has to declare that the &ldquo;child&rdquo; profile must reuse the definitions
from the &ldquo;parent&rdquo; profile.</p>

<pre><code class="language-ruby">profile "Robot do
use_profile RockUGVNav::Profiles::Skid4
end
</code></pre>

<p>However, the whole purpose of reusing profiles in this case is the ability to
leave some &ldquo;inflexion points&rdquo; in profiles, that are then selected for the
particular robot it gets applied to. These inflexion points are defined in the
parent profile with the &lsquo;tag&rsquo; statement:</p>

<pre><code>profile "Skid4" do
# This profile has a tag of the provided type
tag "wheel_control", Services::JointController
# The tag can be used as a service would
define "manual_control", Compositions::ManualControl.
  use('controller' =&gt; wheel_control_tag)
end
</code></pre>

<p>then, when reused, the child profile must say what to use for which tag:</p>

<pre><code>profile "Robot" do
use_profile RockUGVNav::Profiles::Skid4,
  'wheel_control' =&gt; OroGen::Robot::ControllerTask
end
</code></pre>

<p>Ensuring that all the inflexion points of a given profile are properly using
tags is not done every time the profile gets loaded (it would be too costly).
Instead, this is done at testing time with the it_should_be_self_contained
statement (which is enabled by default if you use syskit gen).</p>



</div>
</div>
</div>
