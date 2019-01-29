---
layout: page
title: Error messages
section: Running Components
---
<div class="content2">
<div class="content2-pagetitle">Error messages</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="failed-to-contact-the-name-server-orocoscorbaerror">failed to contact the name server (Orocos::CORBAError)</h2>
<p>The CORBA layer needs a name service to find out what components are available,
and how to contact them. This message appears when this  name service cannot be
contacted. There can be the following reasons:</p>

<ul>
<li>the Orocos::CORBA.name_service attribute, used to specify the hostname of the
name service machine, is set to a wrong value.</li>
<li>there is a firewall blocking TCP port 2809</li>
<li>
<p>the name service is not running on the machine you think it is. You can check
that with</p>

<p>sudo netstat -tl</p>

<p>If the service is running, there should be a line starting with &lsquo;tcp&rsquo; and
ending with 2809/omniNames.</p>
</li>
</ul>

<p><strong>On Debian and Ubuntu</strong>, the OmniORB nameserver startup script does <strong>not</strong>
report an error if the name service startup failed. If you have checked (as
above) that the name service is not running, you can start it by hand to see why
it is not starting with</p>

<p class="commandline">sudo omniNames</p>

<p>Sometimes, the service&rsquo;s log file gets corrupted. In that case, the above
command will yield an error looking like:</p>

<pre>
Error: parse error in log file '/var/lib/omniorb/omninames-mercury.log' at line 1.
</pre>

<p>The best solution then is to simply delete all omninames log files with</p>

<p class="commandline">sudo rm -f /var/lib/omniorb/*</p>



</div>
</div>
</div>
