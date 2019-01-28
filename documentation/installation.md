---
layout: page
hero_text: about Page!
title: Installation
---
<div class="content2">
<div class="content2-pagetitle">Installation</div>

<div class="content2-container line-box">
<div class="content2-container-1col">



<p>This page explains how to install Rock and where to look for more
information (tutorials, &hellip;).</p>

<h2 id="level-of-support">Level of support</h2>
<p>This section lists the operating systems where Rock is <em>well tested</em>, is <em>untested</em> and where the status is <em>unknown</em>.
For <em>well tested</em> operating systems, our <a href="http://rock-robotics.org/status">build server makes sure that Rock builds
fine</a>, and it is known to be actively used. <em>Untested</em> operating systems have
had users (so, it did work at some point), but it is unknown whether it is
still being actively used. Finally, <em>unknown status</em> operating systems are OS&rsquo;s where
Rock should work, but we have had no known report of its success or failure.</p>

<h3 id="well-tested-oss">Well tested OS&rsquo;s</h3>

<table>
  <tbody>
    <tr>
      <td><img src="install/ubuntu.png" alt="Ubuntu" /></td>
      <td>Latest LTS and Latest non-LTS. We let 6 months after a LTS release before deprecating the previous LTS.</td>
    </tr>
    <tr>
      <td><img src="install/debian.png" alt="Debian" /></td>
      <td>testing or unstable</td>
    </tr>
  </tbody>
</table>

<h3 id="experimental-oss">Experimental OS&rsquo;s</h3>

<table>
  <tbody>
    <tr>
      <td><img src="install/gentoo.gif" alt="Gentoo" /></td>
      <td>Last known working version end of 2011</td>
    </tr>
    <tr>
      <td><img src="install/arch.png" alt="Arch" /></td>
      <td>Last known working version end of 2013</td>
    </tr>
    <tr>
      <td><img src="install/opensuse.png" alt="OpenSuse" /></td>
      <td>Beta state, started 2014</td>
    </tr>
    <tr>
      <td><img src="install/fedora.png" alt="Fedora" /></td>
      <td>Last known working version 2012</td>
    </tr>
  </tbody>
</table>

<p>Feel free to ask on the mailinglist to ask for support for porting to another System.
Please let us know any experience if you are using one of the above listed OS&rsquo;s.</p>

<h4 id="legal-comments">Legal Comments</h4>
<p>This site is not affiliated with or endorsed by the Fedora Project.
The Gentoo logo is a trademark of Gentoo Foundation Inc. and the rock-robotic framework is not part
of the Gentoo project, and is not and is not directed or managed by Gentoo Foundation, Inc.
Simliar statements are true for all listed logos and systems. The Rock-Robotic framework itself is
independent from the operation systems and maintained on its own.</p>

<h3 id="status-for-other">Status for other</h3>

<table>
  <tbody>
    <tr>
      <td>Other Linux distributions</td>
      <td>Should work fine. No osdeps.</td>
    </tr>
    <tr>
      <td>MacOS X &copy;</td>
      <td>Known to have problems. No osdeps.</td>
    </tr>
  </tbody>
</table>

<h2 id="installation-the-easy-way">Installation: the easy way</h2>

<ol>
  <li>
    <p>Make sure that the Ruby interpreter is installed on your machine. Rock
requires ruby 2.0 or higher, which is provided on Debian and Ubuntu by
the ruby2.0 package. However we <strong>recommend the use of ruby 2.1</strong> for all OS&rsquo;s
except Ubuntu 14.04, where ruby2.0 should be chosen.</p>

    <pre><code>ruby --version
</code></pre>
  </li>
  <li>Create and &ldquo;cd&rdquo; into the directory in which you want to install the toolchain.</li>
  <li>
    <p>To build the core system of the latest release, use this
<a href="http://www.rock-robotics.org/master/bootstrap.sh">bootstrap.sh</a>
script. Save it in the folder you just created. For other options, see
below.</p>

    <p><strong>There is an important note for long-term Orocos users.</strong> See the red box
below.</p>
  </li>
  <li>
    <p>In a console, run</p>

    <pre><code>sh bootstrap.sh
</code></pre>

    <p>If the command fails (1) report the problem / error to
<a href="http://www.dfki.de/mailman/cgi-bin/listinfo/rock-users">the rock-users mailing
list</a>,
(2) whatever you try to fix it, restart the bootstrapping by doing.</p>

    <pre><code>rm -rf autoproj
sh bootstrap.sh
</code></pre>
  </li>
  <li>Important: as the build tool tells you, you <strong>must</strong> load the generated env.sh script at the end of the build !!!
    <ul>
      <li>
        <p>source it in your current console</p>

        <p class="commandline">. ./env.sh</p>
      </li>
      <li>
        <p>but also add it to your .bashrc: append the following line at the end of
$HOME/.bashrc</p>

        <p class="commandline">. /path/to/the/directory/env.sh</p>
      </li>
    </ul>
  </li>
  <li>Read the <a href="autoproj/basic_usage.html">autoproj guide for basic usage</a> to know the
basic operations of the build system. More bootstrapping documentation is
also available <a href="autoproj/bootstrap.html">at the same place</a></li>
</ol>

<h2 id="other-bootstrapping-options">Other bootstrapping options</h2>
<ul>
  <li><strong>Build the master (development) version of rock</strong>
Use <a href="http://www.rock-robotics.org/master/bootstrap.sh">this bootstrap.sh</a> instead of the one listed above.</li>
  <li>
    <p><strong>Build all of rock</strong>: Call </p>

    <pre><code>autoproj update --all-known-packages
autoproj build --all-known-packages
</code></pre>

    <p>after your basic installation is finished. &lsquo;'&rsquo;This is really meant for continuous integration servers&rsquo;&rsquo;&rsquo;, it does not guarantee
a consistent installation if you forget to add &lsquo;&rsquo;&rsquo;&ndash;all-known-packages&rsquo;&rsquo;&rsquo; to any autoproj call.
If you need a persistent complete installation of rock you must add all metapackages to your layout section.</p>
  </li>
  <li>
    <p><strong>Create a custom version</strong>
Use one of the previous bootstrap scripts to install the base system and then cherry-pick the packages you want. Have a look at <a href="/package_directory.html">the package
directory</a> and add the package names to the
layout section in autoproj/manifest. For instance,  if you want to
get the <a href="../package_directory/packages/drivers_orogen_xsens_imu/index.html">Xsens IMU component</a>,
the layout section should look like:</p>

    <pre><code>layout:
   - rock.core
   - drivers/orogen/xsens_imu
</code></pre>
  </li>
</ul>

<div class="warning">
  <p><strong>Important for existing Orocos users</strong> The development workflow in Rock
currently disables the Orocos deployer and the RTT scripting language by
default, as they are quite expensive on the build times. Select &ldquo;yes&rdquo; at the
&ldquo;compatibility with OCL&rdquo; question during the build to reenable this.</p>
</div>

<h2 id="maintaining-a-rock-installation">Maintaining a Rock installation</h2>

<p>Once Rock is installed, you can update your installation by going to the root of the
installation folder and do</p>

<p class="commandline">autoproj update <br />
autoproj build</p>

<p>You might have to reload the env.sh script after that as well, to export updated environment variables into your current shell. Simply opening a new console will do the trick (given you have added sourcing env.sh to your .bashrc).</p>



              </div>
          </div>
      </div>
