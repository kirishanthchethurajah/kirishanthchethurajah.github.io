---
layout: page
title: Workflow
section: System Management
secondsection: system
---
<div class="content2">
<div class="content2-pagetitle">Workflow</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>This page describes the principles behind the overall development workflow. It
gives some details about the network generation algorithm in the process, as
these details will help understanding each steps of the workflow</p>

<h2 id="step-1-modelling">Step 1: Modelling</h2>
<p>The very first step is to model the components and how the components should be
connected together.</p>

<p>TODO: model browsing screencast</p>

<p>Component models, in Rock, are always done by the component developers when they
use oroGen. The toolchain is designed so that using oroGen is the <strong>only</strong>
constraint that needs to be followed to be able to use Syskit.</p>

<p>Defining how components can be bound together is done by the system designer in
Syskit. It is done by creating so-called <strong>compositions</strong>, which are a
collection of components and connections between these components, necessary to
provide a given function. In order to make these compositions reusable, the
modelling allows you to define <strong>abstract component models</strong> (or so-called data
services). For instance, one would add a Pose service in a composition instead
of referring to a specific component that would provide a pose.</p>

<p>Existing models can be browsed graphically using</p>

<p class="cmdline">syskit browse</p>

<p>TODO: Syskit browse screenshot</p>

<p><strong>System Model</strong>
Example showing the dataflow in the OrientationEstimator Composition from the AVALON AUV student project.</p>

<h2 id="step-2-subsystem-design">Step 2: Subsystem Design</h2>
<p>The second step is to use these composition models to create high-level
functions. Two things are at work there:</p>

<ol>
<li>since compositions can contain data services (abstract component models),
one has to choose exactly which component will provide which service. The
underlying mechanism is akin to the quite common <a href="http://en.wikipedia.org/wiki/Dependency_injection">dependency injection
pattern</a>. Specific
component configurations are also chosen at that point.</li>
<li>deployment choices have to be made, such as on which machine will a component
/ subsystem run, which <a href="../orogen/deployment.html">orocos deployments</a> should
be used and so on.</li>
</ol>

<p>TODO: composer video</p>

<h2 id="step-3-runtime-deployment">Step 3: Runtime deployment</h2>
<p>Finally, you (the user) requires that a set of these subsystems run at a given
point in time. The underlying network generation algorithm will then take care
of <strong>removing redundancies</strong>, i.e. merging components that are part of the same
subsystem together, and finally <strong>adapting</strong> the currently running network to
match the new requirements.</p>

<p>At this stage, a lot of constraint checking is done to ensure that the final
network is <strong>sound</strong>.</p>

<p>TODO: merging video
TODO: runtime adaptation video (avalon ?)</p>

<p>The subsystem design and runtime deployment steps are <strong>fully dynamic</strong>. In
general, the workflow is to design subsystems statically and then switch
between different system configurations (i.e. choice of subsystems) at runtime.
However, it is also possible to create new subsystem definitions at runtime
through e.g. learning mechanisms.</p>

<h2 id="next-pages">Next pages</h2>
<p>The next pages will run you through the creation of models: <a href="data_services.html">data
services</a>, <a href="compositions.html">compositions</a> and <a href="task_contexts.html">task
contexts</a>. Then, we will go on with the description of
subsystems and how they are organized in <a href="profiles.html">profiles</a>. Finally, the
<a href="interaction.html">interaction with a running Roby system</a> using Syskit will be
covered.</p>



</div>
</div>
</div>
