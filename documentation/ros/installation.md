---
layout: page
title: Installation
section: ROS/Rock bridge
---
<div class="content2">
<div class="content2-pagetitle">Installation</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>All the code needed to bridge ROS and Rock is included in a separate package
set. To enable it, you will need to:</p>

<ul>
  <li>have a working ROS installation. The only supported distributions are
groovy and later. Unfortunately, the catkin build system changed too much
between fuerte and groovy to allow us to support fuerte.</li>
  <li>be in an autoproj-based Rock installation</li>
</ul>

<p>In your autoproj manifest (autoproj/manifest) the package_sets section should
contain (at least) the following: </p>

<pre><code> - github: rock-core/package_set
 - gitorious: rock-ros/package_set
</code></pre>

<p>Back in the autoproj manifest (autoproj/manifest) add the following to the
layout section: </p>

<pre><code> - rock.ros
</code></pre>

<p>Now, update and build. This is going to rebuild the oroGen packages as needed</p>

<pre><code>aup --all
amake --all
</code></pre>



</div>
</div>
</div>
