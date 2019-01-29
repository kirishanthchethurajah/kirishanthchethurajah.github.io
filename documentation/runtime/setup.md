---
layout: page
title: Starting the system up
section: Running Components
---
<div class="content2">
<div class="content2-pagetitle">Starting the system up</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="setting-up-corba">Setting up CORBA</h2>

<p>The CORBA communication layer is set up through the Orocos.initialize call. The
general header for an orocos.rb script is therefore:</p>

<pre><code class="language-ruby">require 'orocos'
Orocos.initialize
</code></pre>

<p>It will connect to the default CORBA name service (in general, localhost), and
will raise if the name service can&rsquo;t be found. This default name service will always
be used to register all orocos components started by the ruby instance.</p>

<p>If you want to use another name service, for instance because you have a system
spread on multiple machines and you want to manipulate modules on the various
machines, you can change the default CORBA name service or use multiple name
services at the same time.</p>

<pre><code class="language-ruby">#changing the default CORBA name service

require 'orocos'
Orocos::CORBA.name_service.ip = "host_name"
Orocos.initialize
</code></pre>

<p>Finally, the CORBA.call_timeout and CORBA.connect_timeout
specify timeouts at the CORBA level. See the <a href="../../api/tools/orocos.rb/Orocos/CORBA.html">Orocos::CORBA</a> documentation
for more information.</p>

<h2 id="starting-processes-orogen-based-modules-only">Starting processes <em>(oroGen-based modules only)</em></h2>

<p class="warning"><strong><em>Important</em></strong>: the following functionality is only available for deployments
that have been generated by oroGen.</p>

<p>Orocos.rb has the ability to start deployments that are generated by oroGen
projects. The general syntax for that is:</p>

<pre><code class="language-ruby">Orocos.run 'deployment1', 'deployment2' do
end
</code></pre>

<p>where <tt>deployment1</tt> and <tt>deployment2</tt> are the names of the
deployments. In addition, oroGen creates deployments for each task context you
create, which can be used with:</p>

<pre><code class="language-ruby">Orocos.run 'my_proj::Task' =&gt; 'task_name' do
end
</code></pre>

<p>Orocos.run waits for all the processes to be up and running, and will raise an
exception if one fails to start, i.e. crashes or fails to start within a
specified timeout (20s by default). This timeout can be changed with the :wait
option:</p>

<pre><code class="language-ruby">Orocos.run 'deployment1', 'deployment2', :wait =&gt; 10 do
end
</code></pre>

<p>Orocos.run can also redirect the process outputs to files, and run them in
valgrind. See the <a href="../../api/tools/orocos.rb/Orocos.html">documentation of the Orocos module</a> for more
information.</p>

<h2 id="nameservices">Nameservices</h2>

<p>Three nameservices are currently available: CORBA, Avahi and Local.  All three
can be used in parallel or standalone with any number of instances. To access n
name services with priority order Orocos::NameService can be used bundling the
added name services.</p>

<p>By default there is one Orocos::NameService instance accessible via
Orocos.name_service. And one instance of Orocos::CORBA::NamerService accessible
via Orocos::CORBA.name_service. The latter one is the default CORBA name
service and by default also added to Orocos.name_service.</p>

<p class="info"><strong><em>Note</em></strong>
The default CORBA name service is used to register all Orocos components
started by the ruby instance.</p>

<p class="warning"><strong><em>Important</em></strong>:
Even if the default CORBA name service was removed from the Orocos::name_service instance
it will still be used to register Orocos components started by the ruby instance.</p>

<h3 id="corba">CORBA</h3>
<p>The CORBA name service is usually used to register all running orocos
components of one or multiple machines.</p>

<pre><code class="language-ruby">#changing the default CORBA name service

Orocos.name_service.ip = "host_name"

#adding a second CORBA name service

ns = Orocos::CORBA::NameService.new "host_name2"
# adds the new corba name service to
# the global Orocos::NameService instance
Orocos.name_service &lt;&lt; ns
</code></pre>

<p class="info"><strong><em>Note</em></strong>
Changing the default CORBA name service after Orocos was initialized might have some side effects onto
the accessibility of started orocos components.</p>

<h3 id="avahi">Avahi</h3>
<p>The Avahi nameservice allows distributed name resolution, i.e.
it does not require a centralized server.
For the name resolution via Avahi to work, deployments have to be started
with a service discovery domain set:</p>

<pre><code class="language-ruby">#starting components with Avahi support

Orocos.run 'deployment', 'cmdline_args' =&gt; { 'sd-domain' =&gt; '_robot._tcp' } do |p|
end
</code></pre>

<p>This option is available to deployments
as soon as the tools/service_discovery package has been installed.
Corresponding to the sd-domain the nameservice has to be enabled with
a searchdomain.</p>

<pre><code class="language-ruby"># adding an Avahi name service

ns = Orocos::Avahi::NameService.new "_robot._tcp"
# adds the new corba name service to
# the global Orocos::NameService instance
Orocos.name_service &lt;&lt; ns
</code></pre>

<p class="warning"><strong><em>Note</em></strong>: in order to resolve the task context of the currently started deployment
allow the servicediscovery about one second after deployment start. Use the :wait
option as illustrated in the next section.</p>

<h3 id="local">Local</h3>
<p>The Local name service is used by log replay to register tasks which are replayed from a log file.
It is automatically created and added to the Orocos.name_service instance if a log file is loaded.</p>



</div>
</div>
</div>