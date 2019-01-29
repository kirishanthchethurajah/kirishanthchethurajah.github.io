---
layout: page
title: Data Services
section: System Management
secondsection: system
---
<div class="content2">
<div class="content2-pagetitle">Data Services</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>In Syskit, data services are used as placeholders for &ldquo;actual&rdquo; components. They
allow to create models that e.g. refer to &ldquo;an orientation provider&rdquo; without
actually saying <em>which</em> orientation provider.</p>

<p>Data services represent both an <em>interface</em> (i.e. a set of inputs and outputs)
and a <em>functionality</em> (i.e. what happens to the data). This page will describe
how to define them, and how to represent that, sometimes, certain data services
can used in place of other ones. For instance, that a pose provider can be used
when an orientation provider is needed.</p>

<p>The relationship with other component models (task contexts and compositions)
is described later on these model&rsquo;s respective pages: <a href="compositions.html">compositions</a> and
<a href="task_contexts.html">task contexts</a>.</p>

<p>A data service is defined with</p>

<pre><code class="language-ruby">data_service_type "Orientation" do
  output_port "orientation_samples",
      "base/samples/RigidBodyState"
end
</code></pre>

<p>This defines a Ruby module whose name is given as a string. It is defined in the
local namespace, so one can (and should) organize the data service definitions
using modules:</p>

<pre><code class="language-ruby">module Base
data_service_type 'Orientation' do
  ...
end
end
</code></pre>

<p>which is then referred to as Base::Orientation. The syntax for the input and
output ports is the same <a href="../orogen/task_interface.html#ports">than in oroGen files</a>.</p>

<div class="block">
<p><strong>Generation</strong> Template files for a new data service can be generated with</p>

<pre><code>syskit gen srv subnamespace/name_of_service
</code></pre>

<p>for instance</p>

<pre><code>syskit gen srv localization/pose
</code></pre>

<p>would create the Rock::Services::Localization::Pose service in
models/services/localization/pose.rb file, and update
models/services/localization.rb to require the pose file.</p>
</div>

<p class="block"><strong>Naming</strong> Services should be placed inside ${BundleName}::Services module (e.g.
Rock::Services for the rock bundle)</p>

<p class="block"><strong>Filesystem</strong> Data services are defined in models/services/. Whenever a data
service is used in a syskit file, the file that defines it should be required
first. Types must be loaded using import_types_from &ldquo;project&rdquo;, where &ldquo;project&rdquo;
is the oroGen project that defines the type. Use <a href="general_concept.html#browsing">syskit
browse</a> to find out what is defined where.</p>

<h2 id="model-hierarchy">Model Hierarchy</h2>
<p>The fact that a data service Y can be used instead of another one X (for instance, a Pose
data service that also provides an Orientation), is reflected by telling Syskit
that &ldquo;Y provides X&rdquo;</p>

<pre><code class="language-ruby">data_service_type "Pose" do
output_port "pose_samples", "base/samples/RigidBodyState"
provides Orientation
end
</code></pre>

<p>The resulting data service will have <strong>both</strong> the ports of Y (here,
pose_samples) and the ports of X (orientation_samples) This is clearly not what
is needed here: whenever a Pose service is used in place of an Orientation
service, one would want the pose_samples port to <strong>replace</strong>
orientation_samples. This information must be given explicitly through <em>port
mappings</em>:</p>

<pre><code class="language-ruby">data_service_type "Pose" do
  output_port "pose_samples", "base/samples/RigidBodyState"
  provides Orientation, 'orientation_samples' =&gt; 'pose_samples'
end
</code></pre>

<p>In this definition, the name mapping on the &ldquo;provides&rdquo; stanza specifies that the
&ldquo;orientation_samples&rdquo; port of the provided service (Orientation) should be
replaced by the &ldquo;pose_samples&rdquo; port of the new service.</p>



</div>
</div>
</div>
