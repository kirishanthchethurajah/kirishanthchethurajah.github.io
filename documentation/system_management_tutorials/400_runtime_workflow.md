---
layout: page
title: Runtime Workflow
section: System Management
secondsection: Tutorials
---

<div class="content2">
<div class="content2-pagetitle">Runtime Workflow</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p class="note">The result of this tutorial can be found in bundles/tutorials_scripts if you
followed the instructions at the bottom of <a href="../tutorials/index.html">this page</a></p>

<h2 id="abstract">Abstract</h2>

<p><a href="300_services.html">The previous tutorial</a> showed how to create abstract
compositions, and how to select what should run
(at the point where you are using the &lsquo;add&rsquo; statements).</p>

<p>This tutorial describes a typical development workflow when using Rock&rsquo;s
system management layer:</p>

<ul>
<li>how to restart the deployments when developing the task contexts</li>
<li>how to define multiple subsystems and manage them at runtime</li>
<li>how to inspect a running system</li>
</ul>

<h2 id="starting-and-restarting-processes">Starting and restarting processes</h2>
<p>When developing components, one wants the specific processes to be restarted so
that the changes get picked up. In Syskit, it is done by
using the roby shell.</p>

<p>In a terminal, run scripts/02_services.rb</p>

<p class="cmdline">syskit run scripts/02_services.rb</p>

<p>Then, in another terminal, start the shell</p>

<p class="cmdline">syskit shell</p>

<p>Let&rsquo;s now assume that you changed some code in the
rock_tutorial::RockTutorialControl task and would like to restart the
deployment. You would have to compile and install your changed task, for
instance by doing</p>

<p class="cmdline">amake tutorials/orogen/rock_tutorial</p>

<p>Then, in the Syskit shell we just created, type</p>

<p class="cmdline">syskit.restart_deployments RockTutorial::RockTutorialControl</p>

<p>To restart all deployments, don&rsquo;t provide any argument</p>

<p class="cmdline">syskit.restart_deployments</p>

<p>And, finally, CTRL+C the <code>syskit run</code> script. No need to stop the shell.</p>

<h2 id="defining-modalities">Defining modalities</h2>
<p>Right now, we have two ways (so-called <em>modalities</em>) to control our rock:</p>

<ul>
<li>by using a random motion generator</li>
<li>by using a joystick</li>
</ul>

<p>(Note if you don&rsquo;t have a joystick: you can do most of the tutorials without one
!)</p>

<p>Let&rsquo;s reflect that by <em>defining</em>, i.e. by giving a name to both ways. Copy
scripts/02_services.rb to scripts/03_defines.rb and edit the latter by: (1)
removing the &lsquo;add_mission&rsquo; line at the bottom and (2) adding the following lines:</p>

<pre><code class="language-ruby">define 'joystick', Tutorials::RockControl.use(Controldev::JoystickTask)
define 'random', Tutorials::RockControl.use(TutBrownian::Task)
</code></pre>

<p>Using &lsquo;define&rsquo; allows you to map a composition to a name and reference it later
on using this name. It is
then possible to use the name instead of the complete definition in
the &lsquo;add&rsquo; lines, or in other definition&rsquo;s &lsquo;use()&rsquo; statements. However, using &lsquo;add&rsquo; or &lsquo;use&rsquo; is
not what we&rsquo;re going to do right now. Instead, we are going to use the shell to start these
modalities and switch between them.</p>

<p>Start the application:</p>

<pre><code>syskit run scripts/03_defines.rb
</code></pre>

<p>If you did not already do it, start a shell in a separate terminal:</p>

<pre><code>syskit shell
</code></pre>

<p>The definitions are available as actions in the shell. Run the following command
and look at the top of the output</p>

<pre><code>localhost:48902 &gt; help
Available Actions
=================

Name          | Description
----------------------------------------------
joystick_def! | definition from profile Script
random_def!   | definition from profile Script
</code></pre>

<p>Then run the joystick definition:</p>

<pre><code>localhost:48902 &gt; joystick_def!
</code></pre>

<p>It requests that Syskit starts the joystick-based control, as you can
see in the log or in the live display (if you started it):</p>

<pre><code>localhost:48902 &gt; joystick_def!
=&gt; #&lt;service Roby::Task:0x7fedaea11a00{}[]&gt;
localhost:48902 &gt;
[1] joystick_def! started to plan
[1] joystick_def!: Tutorials::RockControl:0x7fedae9f08f0{}[] has been replaced by Tutorials::RockControl:0x7fedaead30b0{conf =&gt; [default]}[]
[1] joystick_def!: task Tutorials::RockControl:0x7fedaead30b0{conf =&gt; [default]}[] started
</code></pre>

<p>To inspect the set of running actions, just do:</p>

<pre><code>localhost:48902&gt; jobs
1 joystick_def! Tutorials::RockControl:0x54b4a60{conf =&gt; [default]}[]
=&gt;
</code></pre>

<p>The joystick-based control can now be stopped with</p>

<pre><code>localhost:48902&gt; kill_job 1
[1] joystick!: task Tutorials::RockControl:0x7fedaead30b0{conf =&gt; [default]}[] failed
= fatal exception 1: mission failed: Tutorials::RockControl:0x7fedaead30b0{conf =&gt; [default]}[]
| [09:45:47.355 @7] Tutorials::RockControl:0x7fedaead30b0{conf =&gt; [default]}[]/failed
| The following tasks have been killed:
| #&lt;Tutorials::RockControl &lt; Composition &lt; Transformer::CompositionExtension&gt;:0x7fedaead30b0
[1] joystick!: task Tutorials::RockControl:0x7fedaead30b0{conf =&gt; [default]}[] has been removed
</code></pre>

<p>OK, now, let&rsquo;s try to start both the joystick and random control <em>at the same
time</em>.</p>

<pre><code>random_def!
joystick_def!
</code></pre>

<p>Ouch, a lot of cryptic information there. The most interesting bit is at the
top:</p>

<pre><code>= planning joystick_def failed with
| cannot deploy the following tasks
| RockTutorial::RockTutorialControl:0x4714400{conf =&gt; [default]}[]
|   child rock of Tutorials::RockControl:0x46f7120{conf =&gt; [default]}[]
| RockTutorial::RockTutorialControl:0x4714400{conf =&gt; [default]}[]: some deployments exist, but they are already used in this network
|   task rock_tutorial from deployment Deployments::RockTutorial on localhost
|     already used by RockTutorial::RockTutorialControl:0x334ba68{orocos_name =&gt; rock_tutorial, conf =&gt; [default]}[]: child rock of Tutorials::RockControl:0x47432f0{conf =&gt; [default]}[]
</code></pre>

<p>The other error messages are just consequences of this one. This error message
tells you that there actually <em>are</em> some deployments declared for
RockTutorial::RockTutorialControl, but that they are already used.</p>

<p>Indeed, both the random and joystick definitions need a
RockTutorial::RockTutorialControl component. Since they use it in a different
way (the command port gets its data from different sources), Syskit requires
more than one deployment and it fails.</p>

<p>Stop the controller with CTRL+C and the shell with &ldquo;exit&rdquo;, and pass on to the
next tutorial.</p>

<h2 id="summary">Summary</h2>

<p>In this tutorial you learned:</p>

<ul>
<li>how to <em>define</em> some ready-to-deploy networks, and how to use the shell to
start them</li>
<li>how to manage the tasks started through the shell, in particular to support
development of components</li>
<li>what happens if you try to deploy multiple tasks that can&rsquo;t be deployed at
the same time</li>
</ul>

<p>The <a href="500_config_files.html">next tutorial</a> will introduce you to the configuration file management.</p>



</div>
</div>
</div>
