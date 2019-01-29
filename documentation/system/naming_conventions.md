---
layout: page
title: Naming Conventions
section: System Management
secondsection: system
---
<div class="content2">
<div class="content2-pagetitle">Naming Conventions</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>For the process of system modelling we find it very useful to stick to some naming conventions.
These conventions facilitate a sharing of common functionalities and help to avoid nameclashes.
They also allow a quick interpretation of what type of object the developer is dealing with and where is originates from.
By providing a consistent mapping between file structure and class/task name naming it will make the code readable for users and make it easy (short enough) for lazy developers.
Note, however, that the naming schema is not enforce, but needs to actively applied by model developers.</p>

<p>The following naming conventions should be followed when working with Syskit: </p>

<h2 id="component-level-orogen-rtt--ros-tasks">Component level: Orogen (RTT) / ROS Tasks</h2>

<ul>
 <li>OroGen::ObjectDetection::Task
   <ul>
     <li>OroGen::ObjectDetection::Deployments::Test</li>
   </ul>
 </li>
 <li>ROS::ObjectDetection::Task
   <ul>
     <li>ROS::ObjectDetection::Launchers::Test</li>
   </ul>
 </li>
</ul>

<h2 id="supervision-level">Supervision level</h2>
<p>In order to disambiguate between compositions, services, etc. the following convention should
be kept:</p>

<ul>
 <li>NameOfBundle::Compositions::ObjectDetection</li>
 <li>NameOfBundle::Services::ObjectDetection</li>
 <li>NameOfBundle::Devices::ObjectDetection</li>
 <li>NameOfBundle::Profiles::ObjectDetection</li>
 <li>NameOfBundle::Actions::ObjectDetection</li>
 <li>NameOfBundle::Tasks::ObjectDetection</li>
</ul>

<h2 id="file-system-level">File system level</h2>

<p>The following folder structure below the models folder is recommended:</p>

<ul>
 <li>models/orogen/</li>
 <li>models/ros/</li>
 <li>models/compositions/</li>
 <li>models/services/</li>
 <li>models/actions/</li>
 <li>models/profiles/</li>
 <li>models/devices/</li>
</ul>

<p>Using &lsquo;rock-bundle-create&rsquo; will adhere the given naming conventions.</p>

<h2 id="reuse-and-specialization">Reuse and specialization</h2>

<p>In order to deal with specializations that are needed for individual robots the following schema is applied:
* put all generic definitions / functionality into one of the top folders
* add a robot specific subfolder and create a subfolder equalling the robot topfolder&rsquo;s name
* create a corresponding loading file in the robot folder</p>

<p>For example for a robot called &lsquo;artemis&rsquo; that means:
* models/profiles/manipulation.rb
* models/profiles/manipulation/dual_arm.rb
* models/profiles/artemis/manipulation.rb
* models/profiles/artemis/manipulation/dual_arm.rb</p>

<p>The schema allows to maintain the custom ruby behaviour, i.e. the file
&lsquo;profiles/manipulation.rb&rsquo; and &lsquo;profiles/artemis/manipulation.rb&rsquo; loads the subfolders contents.</p>

<pre><code>require 'profiles/manipulation'
require 'profiles/artemis/manipulation/dual_arm'

module Artemis
   module Profiles
       profiles 'Manipulation' do
       end
   end
end
</code></pre>

<p>When using the functionality, e.g. in an action interface &lsquo;models/actions/artemis/manipulation.rb&rsquo;</p>

<pre><code>require 'profiles/artemis/manipulation'
require 'actions/manipulation'

module Artemis
   module Actions
       class Manipulation &lt; Roby::Actions::Interface
           use_profile Artemis::Profiles::Manipulation

           ...
       end
   end
end
</code></pre>



</div>
</div>
</div>
