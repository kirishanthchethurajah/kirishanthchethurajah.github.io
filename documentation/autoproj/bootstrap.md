---
layout: page
title: Bootstrapping
section: Build System
---

<div class="content2">
<div class="content2-pagetitle">Bootstrapping</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="bootstrapping">Bootstrapping</h2>
<p>&ldquo;Bootstrapping&rdquo; means getting, building and installing autoproj before it can be used.
The canonical way is the following:</p>

<ul>
 <li>
   <p>install Ruby by yourself. On Debian or Ubuntu, this is done with
done with</p>

   <p class="cmdline">sudo apt-get install wget ruby1.9</p>

   <p>If you are using an older Ubuntu, make sure that you install Ruby 1.9.3</p>
 </li>
 <li>
   <p>then, <a href="../../autoproj_bootstrap">download this script</a> <em>in the directory where
you want to create an autoproj installation</em>, and run it. This can be done with</p>

   <p class="cmdline">wget http://rock-robotics.org/autoproj_bootstrap <br />
ruby autoproj_bootstrap</p>
 </li>
 <li>
   <p>follow the instructions printed by the script</p>
 </li>
</ul>

<p>Additionally, if you are given a reference to a source code repository in which
an autoproj configuration is stored (i.e. a directory in which a manifest is
present), you can bootstrap this configuration directly:</p>

<p class="cmdline">wget http://rock-robotics.org/autoproj_bootstrap <br />
  ruby autoproj_bootstrap VCS </p>

<p>For instance, to build all packages made available by the Rock project,
do</p>

<p class="cmdline">wget http://rock-robotics.org/autoproj_bootstrap <br />
  ruby autoproj_bootstrap git git://github.com/rock-core/buildconf-all.git</p>

<p>Additional options can be given for the version control system. For instance,</p>

<p class="cmdline">wget http://rock-robotics.org/autoproj_bootstrap <br />
  ruby autoproj_bootstrap git git://github.com/rock-core/buildconf.git branch=test</p>



</div>
</div>
</div>
