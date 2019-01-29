---
layout: page
title: Compositions
section: System Management
secondsection: system
---
<div class="content2">
<div class="content2-pagetitle">Compositions</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>In the system modelling, compositions are what bind components together: they
define, for a specific task, behaviour or subsystem (depending from which side
of robotics you come from), what components are needed and how these components
should be connected together to perform the required function.</p>

<p>In the above description &ndash; as within the rest of this documentation &ndash;
&ldquo;component&rdquo; refers to a data service, task context or composition.</p>

<p>Compositions are defined with</p>

<pre><code class="language-ruby">class ModelName &lt; Syskit::Composition
...
end
</code></pre>

<div class="block">
<p><strong>Generation</strong> A template for a new composition as well as its related unit
tests, following Syskit&rsquo;s naming conventions and file system structure, can be
generated with</p>

<pre><code>syskit gen cmp namespace/name_of_composition
</code></pre>

<p>for instance</p>

<pre><code>syskit gen cmp localization/global_pose_estimator
</code></pre>

<p>in the rock bundle would create the
Rock::Compositions::Localization::GlobalPoseEstimator composition in
models/compositions/localization/global_pose_estimator.rb and the associated
test/compositions/localization/test_global_pose_estimator.rb file in the test
folder. Files in the hierarchy (e.g. models/compositions/localization.rb and
test/compositions/suite_localization.rb) are updated to require the new file(s).</p>
</div>

<p class="block"><strong>Naming</strong> compositions are by convention defined in the
BundleName::Compositions module, and are saved in
models/compositions/name_of_composition.rb. For instance, a
Localization::GlobalPoseEstimator composition in the Rock bundle would be saved
in models/compositions/localization/global_pose_estimator.rb and the full class
name would be Rock::Compositions::Localization::GlobalPoseEstimator. Any task
context, composition or data service used in the composition file should be
required first with either the using_task_library statement (for task contexts)
or a simple require of the relevant file. Use <a href="general_concept.html#browsing">syskit
browse</a> to find out what is defined where.</p>

<p>The main definition statements in a composition are &lsquo;add&rsquo; and &lsquo;connect&rsquo;</p>

<pre><code class="language-ruby">class ModelName &lt; Syskit::Composition
add Base::PoseSrv, :as =&gt; 'pose'
end
</code></pre>

<p>The &ldquo;add&rdquo; line adds a child to the composition, i.e. declares that a component
of that type needs to run to perform the function of this composition. Children
need to be given a name (which should reflect the child&rsquo;s role). For instance,
the rock_ugv_nav bundle defines the SLAM::Odometry composition with:</p>

<pre><code class="language-ruby">module SLAM
class Odometry &lt; Syskit::Composition
add Services::ActuatorControlledSystem, :as =&gt; 'motors'
add Services::Orientation, :as =&gt; 'imu'
add OroGen::Skid4Odometry::Task, :as =&gt; 'odometry'
end
end
</code></pre>

<p>In this example, the composition would require three components:</p>

<ul>
<li>a component providing the Services::ActuatorControlledSystem service, which gives
motor data</li>
<li>a component providing the Services::Orientation service, which gives the
orientation in space of the system whose odometry we&rsquo;re computing</li>
<li>finally, the task context that is going to do the computation (from the
/pkg/slam/orogen/odometry/index.html package).</li>
</ul>

<p>This is all well, but (obviously), these children need to be connected together.
This is done with:</p>

<pre><code class="language-ruby">module SLAM
class Odometry &lt; Syskit::Composition
add Services::ActuatorControlledSystemSrv, :as =&gt; 'motors'
add Services::OrientationSrv, :as =&gt; 'imu'
add OroGen::Skid4Odometry::Task, :as =&gt; 'odometry'

imu_child.connect_to odometry_child
motors_child.connect_to odometry_child
end
end
</code></pre>

<p>The &ldquo;_child&rdquo; statements refer to the defined children (i.e. imu_child for the
imu, odometry_child for the odometry, &hellip;). In the form above, the connect_to
statement will try to create all connections that are possible and unambiguous.
Given an output port, a possible connection is ambiguous if (1) more than one
input port has a matching data type and (2) none of these input ports have the
same name than the output. If no connection is found, an error is generated.</p>

<p>When ambiguous connections exist, one should give the required connection
explicitly by selecting the input port, the output port or both with e.g.</p>

<pre><code class="language-ruby">imu_child.orientation_samples_port.connect_to odometry_child
imu_child.connect_to odometry_child.orientation_samples_port
imu_child.orientation_samples_port.connect_to
odometry_child.orientation_samples_port
</code></pre>

<p>Just as for the child, the ports are referred to as (port_name)_port, i.e.
orientation_samples_port is a port named &ldquo;orientation_samples&rdquo;.</p>

<p>These models can be checked graphically using the Syskit tool. If you have not
yet installed the rock_ugv_nav bundle, do it by calling</p>

<pre><code>aup rock_ugv_nav
</code></pre>

<p>then go in bundles/rock_ugv_nav and run</p>

<pre><code>syskit browse
</code></pre>

<p>On the left side, select the Rock::Compositions::SLAM::Odometry model. The right pane will
show you the following graphical representation of the composition:</p>

<p>TODO: add figure.</p>

<h2 id="providing-services">Providing Services</h2>
<p>In order to be able to use compositions in lieu of services, for instance so
that we can use them in other compositions, we have to give them ports.</p>

<p>Since compositions are only aggregations of other components, they don&rsquo;t have
ports on themselves. They &ldquo;get&rdquo; ports by exporting ports from their children
(one can export both input and output ports this way):</p>

<pre><code class="language-ruby">module SLAM
class Odometry &lt; Syskit::Composition
...
export odometry_child.odometry_delta_samples_port
provides Services::RelativePose
end
end
</code></pre>

<p>As you can see in the example above, the export of a port allows you to then use
the <a href="data_services.html#provides">provides statement</a> to declare that the
composition provides a service and therefore be able to use it whenever said
service is needed.</p>

<h2 id="configuration">Configuration</h2>
<p>In the same way than compositions do not really have ports, but only &ldquo;export&rdquo;
the ports of its children, compositions do not have configurations by
themselves. Instead, configurations are defined as a list of configurations for
the children.</p>

<p>For instance:</p>

<pre><code class="language-ruby">module SLAM
class Odometry &lt; Syskit::Composition
...
conf 'high_speed',
'pose_provider' =&gt; ['default', 'high_sampling_rate']
end
end
</code></pre>

<p>Unlike what is possible with task contexts, one cannot &ldquo;merge&rdquo; configurations on
top of each other (i.e. only one configuration can be selected at a time).
Moreover, the configuration names are not verified at declaration time (in the
example above, &lsquo;pose_provider&rsquo; might be a service and therefore has no notion of
configuration files).</p>

<h2 id="dependency-specification-tasks-and-behaviours-advanced">Dependency specification: tasks and behaviours (advanced)</h2>
<p>When one adds a child to a composition, he also declares that, at runtime, the
composition will need an instance of this child to run. Having a <strong>working</strong>
odometry in a system requires, for instance, a properly configured instance of
OroGen::Skid4Odometry::Task to run. The default dependency setup therefore defines that
there is a dependency constraint failure if the child task stops while the
composition is running. In other words, <a href="../../api/tools/roby/task_relations/dependency.html">Roby&rsquo;s dependency
relation</a> is
used with:</p>

<pre><code class="language-ruby">composition.depends_on(child, :failed =&gt; [:stop])
</code></pre>

<p>The :as option on the #add line is used as the dependency role. It means that,
at runtime, one can access a particular child of the composition by its name:</p>

<pre><code class="language-ruby">composition.odometry_child
</code></pre>

<p>This can be changed at definition time by giving additional parameters to the
add statement. Such a special case is used to change the composition from a
behaviour (runs until not needed) to a goal-oriented task (runs until it
achieved a specified goal). A (hypothetical) Goto, which would use a
TrajectoryFollower::Task to reach a certain goal using a predefined trajectory
could be defined with:</p>

<pre><code class="language-ruby">class Goto &lt; Syskit::Composition
add OroGen::TrajectoryFollower::Task,
:success =&gt; [:reached_the_end],
:as =&gt; 'follower'
end
</code></pre>

<p>where reached_the_end is one of the runtime states reported by the
OroGen::TrajectoryFollower::Task component</p>

<h2 id="extending-the-composition-models">Extending the Composition Models</h2>
<p>Composition models are subclasses of Syskit::Composition which is itself a
grandchild of Roby::Task. As such, much more can be done using the runtime code
execution features of Roby.</p>

<p>For instance, due to the limitations of the composition definition
implementation, one cannot currently use the complete range of event constraints
in the composition definition itself. This has to be done at instanciation time
by overriding Composition.instanciate:</p>

<pre><code class="language-ruby">class Goto &lt; Syskit::Composition
def self.instanciate(plan, *args)
composition = super
composition.follower_child.reached_the_end_event.
forward_to composition.success_event
composition
end
end
</code></pre>

<p>See <a href="../../api/tools/roby">Roby&rsquo;s own documentation</a> for more
information. In general, one would extend compositions with new events, scripts
or poll blocks directly in the block given to composition. For instance:</p>

<pre><code class="language-ruby">class Localization &lt; Syskit::Composition
event :lost
forward :lost =&gt; :failed
end
</code></pre>



</div>
</div>
</div>
