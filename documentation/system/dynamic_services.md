---
layout: page
title: Dynamic Services
section: System Management
secondsection: system
---
<div class="content2">
<div class="content2-pagetitle">Dynamic Services</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>This section hits one of the functionalities that Syskit is the only one to
offer in robotic model-based systems: the ability to deal with <em>dynamic
interfaces</em>.</p>

<p>It is sometimes convenient or event necessary to add the ability, in components,
to create ports at runtime. For instance, certain device drivers would know how
many devices they are attached to only when they access the hardware. Or, motion
task merging paradigms such as itasc or whole body control would create ports as
required by the number of motion tasks they should handle.</p>

<p>In Syskit, this aspect is handled in two parts:
- on the modelling side, by declaring that a given task context (not a
composition) has the ability to create new data services.
- at runtime, by auto-configuring the underlying RTT task context according to
the data services that have been created.</p>

<h2 id="modelling-api">Modelling API</h2>
<p>The dynamic part of a task context interface is represented by declaring that
the task context can create new data services. This is done through by calling
dynamic_service on the task context model:</p>

<pre><code class="language-ruby">class Examples::TaskWithDynamicInterface
dynamic_service AServiceSrv, :as =&gt; 'dispatch' do
# The 'name' local variable contains the name provided
# when instanciating this dynamic service
provides AnotherServiceSrv, :as =&gt; name
end
end
</code></pre>

<p>The service model given to the dynamic_service call declares what kind of
service can be created. The :as option gives a dynamic service name, i.e. a way
to refer to this particular dynamic service when you will want to create one.</p>

<p>Finally, the block is what needs to be performed to instanciate the given
service. It <strong>must</strong> call #provides with the expected service model, or one of
its submodels. Unlike &ldquo;normal&rdquo; #provides calls, ports that this #provides call
expects will be <strong>added</strong> to the interface. Syskit will check that a
corresponding dynamic_input_port / dynamic_output_port stanza exists on the
oroGen model.</p>

<p>New services can then be created with</p>

<pre><code class="language-ruby">model = Examples::TaskWithDynamicInterface.specialize
model.require_dynamic_service('dispatch', :as =&gt; 'service_name')
</code></pre>

<p>The name given to require_dynamic_service is accessible in the dynamic_service
block as the &lsquo;name&rsquo; local variable.  It is customary to use this name in the
corresponding #provides, either plain, or prefixed/suffixed as needed.</p>

<p>The object returned by #with_dynamic_service is a proper task context model. As
such, it can be used everywhere a task context model is used, as for instance
<a href="subsystem_design.html">subsystem definitions</a>. It can e.g. be selected in place of
the base task context:</p>

<pre><code class="language-ruby">AComposition.use(Examples::TaskWithDynamicInterface =&gt; model)
</code></pre>

<p>If you plan to have a more permanent use for this model, assign it to a Ruby
constant:</p>

<pre><code class="language-ruby">model = Examples::TaskWithDynamicInterface.specialize
model.require_dynamic_service('dispatch', :as =&gt; 'service_name')
ConfiguredTask = model
</code></pre>

<p>Since dynamic services are a bit of a &lsquo;raw&rsquo; API, it is common to create an API
on top of it that represents the semantic of the created services. For instance,
assuming that the services we are creating in our examples are used for
monitoring, one could do</p>

<pre><code class="language-ruby">class Examples::TaskWithDynamicInterface
def self.with_monitor(name)
model = specialize
model.require_dynamic_service('dispath', :as =&gt; 'name')
model
end
end
</code></pre>

<p>which then allows to use this interface with</p>

<pre><code class="language-ruby">ConfiguredTask = Examples::TaskWithDynamicInterface.
with_monitor('my_monitor')
</code></pre>

<h2 id="dynamic-services-and-rtt-task-contexts">Dynamic Services and RTT Task Contexts</h2>
<p>What we have seen so far is how to <strong>model</strong> dynamic services. However, since
there is no common protocol on the oroGen task context side (and I don&rsquo;t believe
there could be one), you will need to ensure that the task context gets
configured according to the required dynamic services.</p>

<p>This is done by adding code to the <a href="task_contexts.html#extend-with-code">task&rsquo;s #configure
method</a></p>

<pre><code class="language-ruby">class Examples::TaskWithDynamicInterface
def configure
super # this applies the necessary
     # configuration files. We will
     # override using the dyn srv
     # definitions
each_data_service do |srv|
 # There are currently no way to be sure that 'srv' is dynamic.
 # Use the names to check it out
 #
 # Configuration needs to be stored in task arguments. Note that
 # you can extend the task with new arguments by calling
 # component_model.argument in the block given to dynamic_service

 # Do what is necessary on #orocos_task
 #
 # srv is an instance of Syskit::BoundDataService
 # srv.model is an instance of Syskit::Models::BoundDataService
 if srv.fullfills?(Examples::InputMonitorSrv)
   orocos_task.create_input_monitor srv.name
 elsif srv.fullfills?(Examples::OutputMonitorSrv)
   orocos_task.create_output_monitor srv.name
 end
end
end
end
</code></pre>

<h2 id="complete-example">Complete Example</h2>
<p>In this example, we will model a task context that can have a set of inputs and
merges them based on a given priority. The data type is base/samples/Joints</p>

<pre><code class="language-ruby"># Define the services related to our I/O
import_types_from 'base'
module Base
data_service_type 'JointsProviderSrv' do
output_port 'out', 'base/samples/Joints'
end
data_service_type 'JointsConsumerSrv' do
input_port 'in', 'base/samples/Joints'
end
end

module Merger
class Task
# Declare the merged inputs as a dynamic data serivce
#
# The corresponding orogen description must have
# a dynamic port declaration that matches, e.g.
#
# dynamic_input_port /^in_\w+$/, 'base/samples/Joints'
#
# If the name does not have a fixed pattern, 'nil' can
# be used
#
# dynamic_input_port nil, 'base/samples/Joints'
dynamic_service Base::JointsConsumerSrv, :as =&gt; 'merged_inputs' do
 # Create an argument on the final task to give the priority for this
 # service. It will be retrieved later in the configure method using the
 # service name, i.e.
 #
 #   priority = arguments["#{srv.name}_priority"]
 #
 component_model.argument "#{name}_priority",
   :default =&gt; options[:priority]
 # This service's port will be mapped to a
 # dynamically created in_#{name} port
 #
 # Note that it is possible to also map to static
 # ports !
 provides Base::JointsConsumerSrv, 'in' =&gt; "in_#{name}"
end

# This method is overloaded so that we can autoconfigure the task
# based on the dynamic services that got instanciated. Part of the
# configuration is stored in task arguments, such as the _priority arguments
# that are added in the dynamic_service block above
def configure
 super
 # On this task, the list of inputs is given
 # as a property, so generate the property value
 # and save it
 merging_conf = []
 each_data_service do |srv|
   if srv.fullfills?(Base::JointsConsumerSrv)
     merging_conf &lt;&lt; [srv.name, arguments["#{srv.name}_priority"]]
   end
 end
 orocos_task.merging = merging_conf
end
end

# Define ourselves a composition that is going to
# be the resulting merged joints
class MergedJoints &lt; Syskit::Composition
# The merging task
add Merger::Task, :as =&gt; 'merger'
# The output of the merge allows us to make
# the composition a joint provider itself
export merger_child.merged_joints_port
provides Base::JointsProviderSrv, :as =&gt; 'joints'
end

# Create a composition that merges the given providers
#
# @param [{name =&gt; [Component, Integer]}] a name to subsystem
#   definition mapping that describes the joints providers
# @return a submodel of MergedJoints
def self.merge(providers = Hash.new)
# Allocate the services
task = Merger::Task.specialize
# Define one dynamic service per required provider. The
# providers are given as a mapping of a name to a
# provider service (the data service that will
# give us the data) and a priority.
providers.each do |name, (provider, priority)|
 task.require_dynamic_service 'merged_inputs',
   :as =&gt; name,
   :priority =&gt; (priority || 0)
end
# Create a submodel of the composition, that will
# use our specialized task context
MergedJoints.new_submodel do
 merger = overload 'merger', task
 # Add the children and create the connections
 providers.each do |name, (provider, priority)|
   child = add provider, :as =&gt; name
   # We should be able to refer to the data
   # service here, but it is currently buggy
   # Name the port directly
   child.connect_to merger.find_input_port("in_#{name}")
 end
end
end
end
</code></pre>

<div class="note">
<p>You can test the modelling part of the example above by copy/pasting it to a
file called for instance example.rb and modifying it in the following way:</p>

<ol>
<li>
 <p>modify the &ldquo;class Task&rdquo; first lines to match</p>

 <pre><code class="language-ruby">class Task &lt; Syskit::TaskContext
orogen_model.output_port 'merged_joints',
'base/samples/Joints'
orogen_model.dynamic_input_port /in_\w+/,
'base/samples/Joints'
</code></pre>
</li>
<li>
 <p>add the following lines at the end</p>

 <pre><code class="language-ruby">add_mission(
Merger.merge(
'first' =&gt; [Base::JointsProviderSrv, 0],
'second' =&gt; [Base::JointsProviderSrv, 1]))
</code></pre>
</li>
</ol>

<p>Then, run</p>

<pre><code>syskit instanciate ./example.rb
</code></pre>

<p>to see the result</p>
</div>



</div>
</div>
</div>
