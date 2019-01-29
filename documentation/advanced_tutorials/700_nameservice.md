---
layout: page
title: Using Avahi to find the tasks
section: Advanced
---

<div class="content2">
<div class="content2-pagetitle">Using Avahi to find the tasks</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="abstract">Abstract</h2>
<p>In this tutorial you will learn how to use the Avahi name service that is available with Rock.
This will allow you to distribute your components over multiple physical systems without having to rely on a centralized name service.
The goal of this tutorial is thus: </p>

<ul>
<li>distributing two components, i.e. message_producer and message_consumer will be started independently</li>
<li>connecting two components using the Avahi name service</li>
</ul>

<p>More details on this topic you find in the <a href="../runtime/setup.html">general documentation</a>.</p>

<p>For this tutorial you will reuse the components that you created during the
<a href="../tutorials/index.html#tutorials-outline">basics tutorials</a>.
You can also retrieve them directly by <a href="../tutorials/index.html#installing">installing the tutorial package
set</a></p>

<p class="warning"><strong><em>IMPORTANT</em></strong>:
For this tutorial to work you need to have an +active+ network device,
otherwise Avahi will not publish any information.</p>

<h2 id="ruby-scripts">Ruby scripts</h2>
<p>Until now you might not have questioned how the Ruby scripts establish a connection in details to a task that runs in a deployment.
Orocos.rb used CORBA as one of its transport layers, and it also uses the CORBA name service for finding components.
For that reason, an orocos.rb script has an initialization section, i.e. the following statement are essential.</p>

<pre><code class="language-ruby">require 'orocos'
Orocos.initialize
</code></pre>

<p>By default this initialization sets up the CORBA communication layer, and if no
other statements are made, the CORBA name server on the localhost is used.</p>

<p>You rely on this name service as soon as you call Orocos.name_service.get. The call will
query all available name services to find the task of the given name.</p>

<pre><code class="language-ruby">Orocos.run 'message_producer_deployment' do
  message_producer = Orocos.name_service.get 'message_producer'
  ...
end
</code></pre>

<p>If the task cannot be found, an exception will be raised, e.g. if you
misspelled the task name and used &lsquo;message_producr&rsquo; instead of the correct
name </p>

<h2 id="the-avahi-name-service">The Avahi name service</h2>
<p>Before you can use the Avahi name service you have to make sure the tools/service_discovery package
is installed, since the support for service_discovery is optional.
Only when this package is installed orogen components will be generated with
support for service discovery via Avahi.
The minimal Rock installation already provides this package for you, so you can continue right away.</p>

<p>After being sure service_discovery has been installed and your component has been
build with service discovery support you can instantiate an Avahi name service in your ruby script
with a specific search domain as parameter. For the search domain you can use a
pattern of &lsquo;name&rsquo;.&rsquo;suffix&rsquo;, where the suffix must be either &lsquo;._tcp&rsquo; or &lsquo;._udp&rsquo;.
Here, use &lsquo;_robot._tcp&rsquo;.</p>

<pre><code class="language-ruby">require 'orocos'
Orocos.initialize

# remove the default CORBA name service
# as we do not want to use it for finding
# components
Orocos.name_service.clear
Orocos.name_service &lt;&lt; Orocos::Avahi::NameService.new '_robot._tcp'
</code></pre>

<p>For the name resolution via Avahi to work, you have to start your deployment
within a service discovery domain, and since there exists a command line option
on the deployment to set the service discovery domain (sd-domain), you just
forward the command line option to the deployment.  Be reminded, that this
option is available to deployments +after+ the tools/service_discovery package
has been installed and +after+ (re)building components with the available
tools/service_discovery package.</p>

<pre><code class="language-ruby">Orocos.run 'message_producer_deployment', :cmdline_args =&gt;
{ 'sd-domain' =&gt; '_robot._tcp' }, :wait =&gt; 3 do |p|
...
end
</code></pre>

<p>Since the publishing of the service in the Avahi domain happens with delay once
you start the deployment, you have to allow for a waiting time, before querying
the name service for the name.  By setting the wait option you can pass a time
in second that the script should wait, until executing the block. Select 3
seconds until the actual block of the Ruby script starts to run. </p>

<p>After enabling the message producer startup to use Avahi, also activate the
Avahi name service for the message consumer.  The message producer will be
started separately, thus remove the message_producer_deployment from the
start.rb in the message consumer component.</p>

<pre><code class="language-ruby">require 'orocos'
require 'readline'

Orocos.initialize
Orocos.name_service.clear
Orocos.name_service &lt;&lt; Orocos::Avahi::NameService.new '_robot._tcp'

Orocos.run 'message_consumer_deployment', :cmdline_args =&gt;
{ 'sd-domain' =&gt; '_robot._tcp'}, :wait =&gt; 3 do

   message_producer = Orocos.name_service.get 'message_producer'
   message_consumer = Orocos.name_service.get 'message_consumer'

   message_consumer.start

   message_producer.messages.connect_to message_consumer.messages

   Readline::readline("Press ENTER to exit\n") do
   end
end
</code></pre>

<h3 id="run-it">Run it</h3>
<p>First start the message producer, then the message consumer. The message consumer will output the message in
the same way a before, however, this time both component found each other using the Avahi naming service. </p>

<pre><code class="language-text">ruby message_producer/scripts/start.rb &amp;
ruby message_consumer/scripts/start.rb
</code></pre>

<p><strong><em>NOTE</em></strong>: You need to have a proper configuration of Avahi, and there are some known issue with using IPv4.
Enabling IPv6 for the daemon in /etc/avahi/avahi-daemon.conf will help to make service discovery more robust.</p>

<pre><code class="language-text">[server]
...
use-ipv6=yes
...

[publish]
publish-a-on-ipv6=yes
...
</code></pre>

<h2 id="summary">Summary</h2>
<p>In this tutorial you have learned to:</p>

<ul>
<li>enable the Avahi name service for finding running tasks</li>
<li>apply options to the Ruby script </li>
<li>connect two components using the Avahi name service</li>
</ul>


</div>
</div>
</div>
