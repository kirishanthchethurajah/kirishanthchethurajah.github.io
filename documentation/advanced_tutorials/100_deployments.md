---
layout: page
title: Deployments
section: Advanced
---

<div class="content2">

<div class="content2-pagetitle">Deployments</div>

<div class="content2-container line-box">
<div class="content2-container-1col">



<p>Conceptually, the execution of Orocos tasks is split into two parts. First, the
task encapsulates the so-called &ldquo;computation&rdquo; part of the component. It is
declared and developed, but <em>how</em> it should be executed is left out. Second, it
is <strong>deployed</strong>: The task is associated with a <em>trigger mechanism</em> that defines
when the task&rsquo;s computation should be executed.</p>

<p>Until now, you have used deployments implicitly as, for each task, oroGen
defines a default deployment for convenience reasons. The default deployment
execution schema is given in the task description by statements like
<a href="../tutorials/110_basics_create_component.html">periodic(1.0)</a> or
<a href="../tutorials/130_basics_connect_components.html">port_driven</a>.
Furthermore, the default deployments add a logging component by default and each
task is run as a separate process.</p>

<p>In order to allow a more flexible definition of the execution schema and putting multiple
tasks into one process, explicit deployments can also be specified. They
override the default deployment of the task. For further information, also have a look at the
<a href="../orogen/deployment.html">documentation page</a>.</p>

<p>Deployments are specified in an orogen project, the same way as tasks are.
Even though it is possible to define deployments in the same orogen file as the
tasks, that is not recommended. Best practice is to create a separate oroGen
project for all your deployments, in order to keep an overview of how all the
tasks on your system are defined.</p>

<h2 id="defining-deployments">Defining deployments</h2>

<p>Let&rsquo;s get back to the <a href="../tutorials/110_basics_create_component.html">initial messenger example</a>.
The periodicity was set to 1.0 seconds for the default deployment. Let&rsquo;s assume
now that the task should be executed at a higher rate. Create a new oroGen
project (for instance in tutorials/orogen):</p>

<pre><code class="language-text">rock-create-orogen messenger_deployments
</code></pre>

<p>When asked for dependencies, give the message producer and consumer task projects, e.g.:</p>

<pre><code class="language-text">tutorials/orogen/message_producer, tutorials/orogen/message_consumer
</code></pre>

<p>In the file <em>messenger_deployments.orogen</em> delete the content and add the following:</p>

<pre><code class="language-ruby">name "messenger_deployments"

using_task_library "message_producer"

deployment "faster_message_producer" do
task("producer","message_producer::Task").periodic(0.1)
end
</code></pre>

<p>Finalize the package creation with (in the same directory than the oroGen file)</p>

<pre><code>rock-create-orogen
</code></pre>

<p>And, finally, the usual</p>

<pre><code class="language-text">amake
</code></pre>

<h2 id="usage-in-ruby-scripts">Usage in Ruby scripts</h2>
<p>The defined deployments are used in Ruby scripts by replacing the model_name =&gt;
task_name syntax with the deployment name. Moreover, the <code>Orocos.name_service.get</code> stanzas
must use the task names declared in the specific deployments (first argument to
the &ldquo;task&rdquo; statement, as e.g. producer above). Using the explicit deployment above
for the <a href="../tutorials/130_basics_connect_components.html">messenger example
script</a>, while keeping the default
deployment for the consumer would be done with:</p>

<pre><code class="language-ruby">require 'orocos'
require 'readline'

include Orocos
Orocos.initialize

Orocos.run 'faster_message_producer',
'messages::Consumer' =&gt; 'consumer' do

# When using explicit deployments, the name that should be used in
# Orocos.name_service.get must be the same name than given as first argument of the
# "task" statement in the deployment definition
producer = Orocos.name_service.get 'producer'
consumer = Orocos.name_service.get 'consumer'

# Never assume that a component supports being reconnected
# at runtime, it might not be the case
#
# If you have the choice, connect before the component is
# even configured
producer.messages.connect_to consumer.messages

producer.configure
producer.start

consumer.configure
consumer.start

Readline::readline("Press ENTER to exit\n")

end
</code></pre>



</div>
</div>
</div>
