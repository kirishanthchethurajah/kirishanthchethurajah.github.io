---
layout: page
title: Configuration files
section: System Management
secondsection: Tutorials
---

<div class="content2">
<div class="content2-pagetitle">Configuration files</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p class="note">The result of this tutorial can be found in bundles/tutorials_scripts if you
followed the instructions at the bottom of <a href="../tutorials/index.html">this page</a></p>

<h2 id="abstract">Abstract</h2>
<p>This tutorial will show you how to use configuration files within bundles and how to apply configurations
to compositions and components.</p>

<h2 id="presentation">Presentation</h2>
<p>Configuration files are YAML files in which the properties are listed and some
values are assigned to them. For instance, when looking at the interface of the
random motion generator:</p>

<pre><code>[...er/bundles/tutorial]% rock-inspect --show-tasks tut_brownian
==========================================================
Task name:  tut_brownian::Task
defined in tut_brownian
----------------------------------------------------------

 ------- tut_brownian::Task ------
 no documentation defined for this task context model
 subclass of RTT::TaskContext (the superclass elements are displayed below)
 Ports
   [out]cmd:/base/MotionCommand2D
   [out]state:/int32_t
 No dynamic ports
 Properties
   max_angular_speed:/double, default: 0.314159265358979
   max_speed:/double, default: 1.5
   min_speed:/double, default: 0.5
   straight_duration:/double, default: 5
   turn_duration:/double, default: 5
 No attributes
 No operations
</code></pre>

<p>We have 5 properties that configure our component. Let&rsquo;s use a non-default value for the max_speed.</p>

<p>First, let&rsquo;s use a tool to generate a file filled with default values. To do this, we first
create configuration directory for our oroGen components in bundles/tutorials and then use the
tool <em>oroconf</em> to generate default configuration files with the option:</p>

<pre><code>oroconf extract tut_brownian::Task
</code></pre>

<p>The new template file is generated in the config/orogen subfolder of the bundle,
with a file name that matches the oroGen model name (here,
config/orogen/tut_brownian::Task.yml)</p>

<p class="warning">The YAML files in config/orogen/ <strong>must</strong> follow the modelname.yml template to be
picked up by the system. In this case, the generated file has automatically be
called tut_brownian::Task.yml</p>

<p>Have a look at the freshly created config/orogen/tut_brownian::Task.yml, and
compare it to the values reported by rock-inspect.</p>

<p>Let&rsquo;s now start a controller and start the &lsquo;random&rsquo; definition. And have a look
at the configuration values using rock-display. They should match the values in
the configuration file.</p>

<pre><code>syskit run scripts/03_defines.rb random_def!
</code></pre>

<p class="note">As you see here, it is possible to specify definitions on the command line of
<code>syskit run</code>, to avoid having to go through the shell for simple cases.</p>

<h2 id="multiple-component-configurations">Multiple Component Configurations</h2>
<p>The configuration files can contain multiple blocks separated by three dashes
(&mdash;) and a name. Let&rsquo;s add two new configurations. Change
config/orogen/tut_brownian::Task.yml to look like:</p>

<pre><code class="language-yaml"> --- name:default
 # no documentation available for this property
 max_angular_speed: 0.314159265358979
 # no documentation available for this property
 max_speed: 1.5
 # no documentation available for this property
 min_speed: 0.5
 # no documentation available for this property
 straight_duration: 5.0
 # no documentation available for this property
 turn_duration: 5.0
 --- name:slow
 max_speed: 0.5
 min_speed: 0
 --- name:fast
 max_speed: 4
 min_speed: 2
</code></pre>

<p>If nothing else is specified, the &lsquo;default&rsquo; configuration is applied to newly
configured components. If it does not exist, then no properties are written.
When a different configuration is needed, <strong>configuration overlays</strong> can be
selected: the parameters defined by some sections are overriden by other(s). For
instance, the &lsquo;default,slow&rsquo; configuration contains all values from default
except for max_speed and min_speed that are set to the values in the &lsquo;slow&rsquo;
configuration.</p>

<p>In a definition, the requested configuration is selected with</p>

<pre><code class="language-ruby">define 'random_slow', Tutorials::RockControl.use(TutBrownian::Task.use_conf('default', 'slow'))
</code></pre>

<p>Which configuration is actually applied can be visualized in <code>syskit
instanciate</code> (the conf: flag) and in roby-display (simply double-click on the
task you are interested in to see its arguments). Finally, you can see it in the
console output of <code>syskit run</code> (only the relevant output lines are shown below).</p>

<pre><code># syskit run 03_defines.rb random_slow_def!
Bundles[INFO]: Active bundles: tutorial
/media/Data/dev/rock/master-full/tools/syskit/lib/syskit/models/deployment.rb:83: warning: already initialized constant Joystick
20:19:20.540 (Robot) applied configuration [default, slow] to /brownian
20:19:20.540 (Robot) setting up TutBrownian::Task:0x2d69690{orocos_name =&gt; brownian, conf =&gt; [default, slow]}[]
</code></pre>

<h2 id="multiple-composition-configurations">Multiple Composition Configurations</h2>
<p>The information in the previous section covered the components.  One can also
define configurations on compositions. Since compositions do not actually have
properties, they are defined differently though. Indeed, a composition
configuration specify how the composition&rsquo;s children should be configured (i.e.
it &ldquo;forwards&rdquo; some configuration selection).  For instance, the following
declaration announces that Tutorials::RockControl has a &lsquo;slow&rsquo; configuration,
in which the &lsquo;cmd&rsquo; child (the Tutorials::CommandGeneratorSrv) should be
configured with &lsquo;default,slow&rsquo;. The configuration of the RockTutorialControl
task is left open.</p>

<pre><code class="language-ruby">module Tutorials
  class RockControl &lt; Syskit::Composition
    # Any command generator
    add CommandGeneratorSrv, :as =&gt; "cmd"
    # And one rock
    add RockTutorial::RockTutorialControl, :as =&gt; "rock"
    # Create any unique connection possible, by matching
    # input and output ports of the same data type. If
    # ambiguities exist, an error is generated
    cmd_child.connect_to rock_child

    conf 'slow', cmd_child =&gt; ['default', 'slow']
  end
end
</code></pre>

<p>These configurations are selected in the same way. Note, however, that they cannot be overlaid.</p>

<pre><code class="language-ruby">define 'random_slow2',
  Tutorials::RockControl.use(TutBrownian::Task).use_conf('slow')
</code></pre>

<p class="note"><strong>Advanced</strong> The configurations are part of the deployment constraints. This means, in
practice that if two deployments request a task to be deployed with two different
configurations, the deployment will fail.</p>

<p>There is, for now, no ways to select configurations from the shell.</p>

<h2 id="summary">Summary</h2>
<p>In this tutorial you learned:</p>

<ul>
  <li>how to generate configuration files</li>
  <li>how to make them part of your definitions, at both the component and composition levels</li>
</ul>

<p>The <a href="600_more_complex.html">next tutorial</a> will introduce you to handling some more complex setups.</p>



</div>
</div>
</div>
