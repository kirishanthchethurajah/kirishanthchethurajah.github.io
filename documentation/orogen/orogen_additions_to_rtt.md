---
layout: page
title: Advanced - oroGen additions to the standard RTT component interface
section: Developing Components
---
<div class="content2">
<div class="content2-pagetitle">Advanced - oroGen additions to the standard RTT component interface</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>For the Rock toolchain to work, there are a few things that all oroGen component
have that normal RTT components do <em>not</em> have. This page is a reference to these
&ldquo;additions&rdquo;, for RTT developers that want to understand what is different
between a normal RTT component and an oroGen component.</p>

<ol>
  <li>
    <p>a getModelName operation is defined on all oroGen components. This
operation returns the full model name for the task (in practice, the class
name). So, for instance, the &ldquo;Task&rdquo; task context in the hokuyo oroGen
project will return hokuyo::Task.</p>

    <p>This is implemented so that orocos.rb is able to automatically load oroGen
specification files when contacting a task context, regardless of how this
task context has been deployed and/or started.</p>
  </li>
  <li>
    <p>a &lsquo;state&rsquo; port with type &lsquo;int&rsquo; is defined, in which all the state
transitions are exported. The allowed values in this port are defined by an
oroGen-generated enumeration called orogen_project::task_name_STATES. (e.g.
hokuyo::Task_STATES). The management of it is done transparently by
orocos.rb.</p>
  </li>
  <li>
    <p>all known input ports are automatically cleared in startHook()</p>
  </li>
</ol>



</div>
</div>
</div>
