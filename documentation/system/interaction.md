---
layout: page
title: Running and interacting with a Roby/Syskit system
section: System Management
secondsection: system
---
<div class="content2">
<div class="content2-pagetitle">Running and interacting with a Roby/Syskit system</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>Syskit bundles are first and foremost Roby applications. As such, the tools and
methods to interact with a Roby application also apply to the ones that are
using Syskit. You should therefore have first a look at what can be done with a
Roby application. In particular, you should look at the <a href="/api/tools/roby/building/actions.html">Roby action
interface</a> and the ways <a href="/api/tools/roby/interacting/index.html">one can interact
with a running Roby application</a></p>

<p>What this page is going to be about is how Syskit fits inside these concepts:
how <a href="profiles.html">profiles</a> are mapped to Roby <a href="/api/roby/building/actions.html">actions</a>, and what it means to deploy subsystems through
the action interface.</p>

<h2 id="using-profiles-as-roby-actions">Using profiles as Roby actions</h2>
<p><em>In fine</em>, when using Syskit, one designs a &ldquo;master&rdquo; profile in which all the
behaviours / needed subsystems are defined. This master profile can then be
injected in an action interface class (usually the main interface in
models/actions/main.rb) with</p>

<pre><code class="language-ruby">class Main &lt; Roby::Actions::Interface
use_profile MyProject::MyMasterProfile
end
</code></pre>

<p>This defines actions for all <a href="profiles.html">definitions</a> and
<a href="devices.html">devices</a> this profile defines. The definitions are suffixed with
_def and the devices with _dev.</p>

<h2 id="deploying-subsystems-through-the-action-interface">Deploying subsystems through the action interface</h2>

<p>Since definitions and devices are now actions, they can be <a href="/api/tools/roby/interacting/index.html">used normally in the
action interface</a>. One can
therefore start a device (for instance for a small data gathering session) on
the command line with:</p>

<pre><code>roby run -rmyrobot left_camera_dev!
</code></pre>

<p>Alternatively, the shell interface allows to start and stop specific
subsystems using the action interface and job systems. Start a shell to connect
to a running Syskit/Roby controller with</p>

<pre><code>roby shell
</code></pre>

<p>and then:</p>

<pre><code>left_camera_dev!
jobs
kill_job 1
</code></pre>

<p>Finally, one can add actions-as-missions in the code (for instance in the
controller file to start some services &ldquo;by default&rdquo;) with</p>

<pre><code class="language-ruby">Robot.left_camera_dev!
</code></pre>

<p>What is important to understand at this point is that Syskit compute the
<strong>merged</strong> network of all the required subsystems. I.e. if you start two big
networks that <em>share</em> parts, these parts will actually <strong>be</strong> shared in the final
component network, without any additional work from your part. This video tries
to explain this process:</p>

<p>TODO: video</p>

<p>What happens if you try to start a new new subsystem that <strong>cannot</strong> be merged with the
running network ? This happens for instance if the two networks are trying to use
the same device in different configurations, or if they have incompatible <a href="../data_processing/transformer_roby.html">frame
transformation configurations</a>
When this happens, the <strong>newly started</strong> network will not be deployed, and the
rest will stay the same. The error message will try to give you <strong>some</strong>
insights as to why the deployment is not possible, but we have to admit that,
right now, the explanation requires quite a bit of thinking on your part. This
is something we try to improve in the long run.</p>



</div>
</div>
</div>
