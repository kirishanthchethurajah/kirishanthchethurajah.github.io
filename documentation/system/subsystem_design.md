---
layout: page
title: Subsystem Design
section: System Management
secondsection: system
---
<div class="content2">
<div class="content2-pagetitle">Subsystem Design</div>
<div class="content2-container line-box">
<div class="content2-container-1col">


                 <p>What we saw so far is the ability to <strong>model</strong> how things should run together.
Data Services allow to represent abstract components. Compositions, to bind
components together to create some functionality, and Task Contexts represent
the actual implementations.</p>

<p>However, there is still the problem of telling the system what should <strong>actually
run</strong>. This step is called <em>system deployment</em>.<br />
At the root of everything are the <strong>instance requirements</strong>. They describe a
subsystem so that the Syskit engine can create the network associated with
it. In essence, when creating instance requirements, one picks up what should
run where. It represents, for instance, &lsquo;I want a PoseEstimation composition where the
orientation child, which is originally declared as an orientation service, is
replaced by a XsensImu::Task task context&rsquo;</p>

<p>Then, these requirements are used in Roby&rsquo;s action interface to allow to start
and stop complete subsystems dynamically. This is going to be presented in the
following pages.</p>

<h2 id="instance-requirements">Instance Requirements</h2>
<p>The instance requirements describe a subsystem that you require to run. The
simplest of all is a single component model (either task or composition). For
instance:</p>

<pre><code class="language-ruby">OroGen::CorridorNavigation::FollowingTask
</code></pre>

<p>tells the system that you require a component of type
CorridorNavigation::FollowingTask to be running, not giving any other
constraints.</p>

<p>Compositions need a bit more information, as you might need to specify
composition children. For instance, the composition that handles the
CorridorNavigation::FollowingTask above requires a full robot pose, provided by
a Base::PoseSrv data service.</p>

<p>TODO: image</p>

<p>Instanciating it therefore requires to tell the system what should be used to
provide this pose. This is done by the #use statement:</p>

<pre><code class="language-ruby">Compositions::CorridorNavigation::Servoing.
 use('pose' =&gt; PoseEstimation)
</code></pre>

<p>where &lsquo;pose&rsquo; is the name of the child that is being selected. This name is given
<a href="compositions.html">in the child declaration</a> with the :as option.</p>

<p>This is assuming that PoseEstimation is a composition that can be instanciated
&ldquo;by itself&rdquo; (i.e. needs no other information to be instanciated). If it was
needing some more specification, you would specify it the same way (don&rsquo;t worry,
there are better ways to write that down)</p>

<pre><code class="language-ruby">Compositions::CorridorNavigation::Servoing.
 use('pose' =&gt; PoseEstimation.
   use('orientation' =&gt; XsensImu::Task)
 )
</code></pre>

<p>The other thing that can be specified in an instance requirement are the task
arguments (as in <a href="/api/tools/roby/building/task_models.html">Roby task
arguments</a>).
This is simply done with #with_arguments:</p>

<pre><code class="language-ruby">Compositions::CorridorNavigation::Servoing.
 with_arguments(:target_point =&gt; Eigen::Vector3.new(10, 12, 0)).
 use('pose' =&gt; PoseEstimation.
   use('orientation' =&gt; XsensImu::Task)
 )
</code></pre>

<p>Finally, a special case of task arguments is the handling of the <a href="task_contexts.html#configuration">task
contexts configuration</a>. One specifies it with #with_conf:</p>

<pre><code class="language-ruby">Compositions::CorridorNavigation::Servoing.
 with_arguments(:target_point =&gt; Eigen::Vector3.new(10, 12, 0)).
 use('pose' =&gt; PoseEstimation.
   use('orientation' =&gt; XsensImu::Task.
       with_conf('default', 'high_sampling_rate')
   )
 )
</code></pre>

<p>Finally, when selecting a composition child and/or selecting something for a
service, one might need to select a service explicitly (for instance because the
selected composition / task provides multiple services of the same kind). This
is done by providing the service as selection, instead of only the task.</p>

<pre><code class="language-ruby">Compositions::CorridorNavigation::Servoing.
 with_arguments(:target_point =&gt; Eigen::Vector3.new(10, 12, 0)).
 use('pose' =&gt; PoseEstimation.pose_srv)
</code></pre>

<p>when done on a full instance requirement object, it has to be done at the end
(e.g. after the use() and other statements)</p>

<pre><code class="language-ruby">Compositions::CorridorNavigation::Servoing.
 with_arguments(:target_point =&gt; Eigen::Vector3.new(10, 12, 0)).
 use('pose' =&gt; PoseEstimation.
   use('orientation' =&gt; XsensImu::Task.
       with_conf('default', 'high_sampling_rate')
   ).pose_srv
 )
</code></pre>

<h2 id="summary">Summary</h2>
<p>This page explained the general syntax and concepts related to dependency
injection in Syskit. However, you probably noticed how it could become pretty
tedious for complex systems.</p>

<p>To promote reuse of <em>configured subsystems</em>, as well as allowing to <em>name
subsystems</em>, Syskit introduces the concept of <strong>profiles</strong>. This is the subject
of <a href="profiles.html">the next page</a></p>

<p>In general, have a look at the documentation for
<a href="/api/tools/syskit//Syskit/InstanceRequirements.html">Syskit::InstanceRequirements</a> to get all the available specifications that can
be added to an instance requirement object.</p>


</div>
</div>
</div>
