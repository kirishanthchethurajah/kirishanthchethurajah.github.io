---
layout: page
title: Connecting components
section: Basics
---

<div class="content2">

<div class="content2-pagetitle">Connecting components</div>

<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="abstract">Abstract</h2>
<p>This tutorial will teach you:</p>

<ul>
 <li>How to create a data driven component and</li>
 <li>how to connect multiple components.</li>
</ul>

<p>The data driven component that is designed in this tutorial waits for incoming messages and outputs their
content once it receives a message. The incoming messages will be produced by the producer component
from the previous tutorials.</p>

<p>The final setup will look like:</p>

<p class="align-center"><img src="130_producer_consumer.png" alt="Producer/Consumer Setup" /></p>

<h2 id="create-a-consumer-component">Create a consumer component</h2>

<p>The consumer component has already been created together with the producer
component in <a href="110_basics_create_component.html">one of the previous tutorials</a>.</p>

<p>The consumer component&rsquo;s part of the orogen specification
(<em>~/dev/tutorials/orogen/messages/messages.orogen</em>)
looks as follows:</p>

<pre><code class="language-ruby">task_context "Consumer" do
 needs_configuration

 property "config", "message_driver/Config"

 input_port "messages", "message_driver/Message"
 port_driven 'messages'
end
</code></pre>

<p>The component should allow to receive messages on an input port, and to call
the updateHook only when it receives a message.</p>

<p>The &lsquo;port_driven&rsquo; statement allows you to specify ports, where incoming data
triggers a call to the updateHook of the task (more details in the
<a href="../orogen/triggering/ports.html">documentation</a>). </p>

<p>Update the consumer component&rsquo;s header file as follows:</p>

<p><em>~/dev/tutorials/messages/tasks/Consumer.hpp:</em></p>

<pre><code class="language-cpp">#include &lt;message_driver/MessageDriver.hpp&gt;
...
   class Consumer : public ConsumerBase
   {
 friend class ConsumerBase;
   protected:

       message_driver::MessageDriver *mpMessageDriver;
</code></pre>

<p>In the consumer component&rsquo;s source file, update the constructors as follows:</p>

<p><em>~/dev/tutorials/messages/tasks/Consumer.cpp:</em></p>

<pre><code class="language-cpp">Consumer::Consumer(std::string const&amp; name)
   : ConsumerBase(name),
   mpMessageDriver(new message_driver::MessageDriver())
{
}

Consumer::Consumer(std::string const&amp; name, RTT::ExecutionEngine* engine)
   : ConsumerBase(name, engine),
   mpMessageDriver(new message_driver::MessageDriver())
{
}

Consumer::~Consumer()
{
   delete mpMessageDriver;
}
</code></pre>

<p>In the same file, implement the updateHook,
so that every incoming message is printed to stdout, using the existing
printMessage function of your &lsquo;message_driver&rsquo; library.</p>

<pre><code class="language-cpp">void Consumer::updateHook()
{
   ConsumerBase::updateHook();

   message_driver::Message message;
   _messages.read(message);

   mpMessageDriver-&gt;printMessage(message);
}
</code></pre>

<p>Build your component with &lsquo;amake&rsquo;.</p>

<h2 id="connecting-multiple-components">Connecting multiple components</h2>

<p>You have designed a component that produces messages and a component that
consumes messages. To connect them, you will use the Ruby scripting interface.
Create a file <em>connect.rb</em> in your component&rsquo;s script folder
(<em>~/dev/tutorials/orogen/messages/scripts</em>) with the following content: </p>

<pre><code class="language-ruby">require 'orocos'
require 'readline'

include Orocos
Orocos.initialize


Orocos.run 'messages::Producer' =&gt; 'message_producer',
   'messages::Consumer' =&gt; 'message_consumer' do

 message_producer = Orocos.name_service.get 'message_producer'
 message_consumer = Orocos.name_service.get 'message_consumer'

 # Never assume that a component supports being reconnected
 # at runtime, it might not be the case
 #
 # If you have the choice, connect before the component is
 # even configured
 message_producer.messages.connect_to message_consumer.messages

 message_producer.configure
 message_producer.start

 message_consumer.configure
 message_consumer.start

 Readline::readline("Press ENTER to exit\n")
end
</code></pre>

<p>The call to &lsquo;connect_to&rsquo; for an output port allows you to connect it with an input port. By default, a data connection is created, but you can also specify the type of your connection explicitly. Check the <a href="../runtime/ports.html">documentation</a> for more details on that topic. </p>

<h3 id="run-it">Run it</h3>

<p>Run your ruby script</p>

<pre><code class="language-cpp">ruby connect.rb
</code></pre>

<p>If everything has been done correctly, you will eventually see the consumer printing messages to the console, in the periodicity you set on the message producer: </p>

<pre><code class="language-cpp">[20110803-10:59:55:068] Message from MessageDriver
[20110803-10:59:56:068] Message from MessageDriver
[20110803-10:59:57:068] Message from MessageDriver
[20110803-10:59:58:068] Message from MessageDriver
</code></pre>

<p>Press ENTER to shut your script down &ndash; the script does the cleanup itself once it leaves the Orocos.run block.</p>

<h2 id="summary">Summary</h2>
<p>In this tutorial, you have learned to: </p>

<ul>
 <li>Create a component that is triggered upon receiving incoming data, i.e. you know now how to design a port/data driven component.</li>
 <li>Run two components using a single ruby script.</li>
 <li>Connect two components by connecting an output and an input port with a default data connection.</li>
</ul>

<p>Progress to the <a href="190_installing_packages.html">next tutorial</a>.</p>



</div>
</div>
</div>
