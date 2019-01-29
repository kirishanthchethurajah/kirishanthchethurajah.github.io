---
layout: page
title: Basic usage
section: Build System
---

<div class="content2">
<div class="content2-pagetitle">Basic usage</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p><strong>Building the installation and packages:</strong></p>

<p>To build all packages, simply do:</p>

<p class="commandline">autoproj build</p>

<p>It will ask the value of newly defined configuration options, import code hosted
remotely that is not yet present, install OS packages (e.g. Ubuntu packages on
an Ubuntu installation) and build everything.</p>

<p>If you want to build only a specific package you can use, here it for building &lsquo;orocos/rtt&rsquo;:</p>

<p class="commandline">autoproj build orocos/rtt</p>

<p>Alternatively to autoproj build, you can use the &lsquo;short&rsquo; command version:</p>

<p class="commandline">amake </p>

<p>It runs autoproj build for the given directory or package name.
Selects the current directory if none is given</p>

<p><strong>Updating and maintaining your installation:</strong></p>

<p>In order to update your packages:</p>

<p class="commandline">autoproj update</p>

<p>Alternatively, you can use the &lsquo;short&rsquo; command version:</p>

<p class="commandline">aup </p>

<p>It runs autoproj update for the given directory or package name.</p>

<p><strong>Rebuilding your installation:</strong></p>

<p>If for some rare reason, you need to rebuild your stack, then do</p>

<p class="commandline">autoproj rebuild</p>

<p>A less intrusive version of it only forces all tools to reconsider building. It
is mainly useful for CMake when the build environment changed &ndash; CMake caches a
lot of values. To trigger this, do</p>

<p class="commandline">autoproj force-build</p>

<p class="warning"><strong>Exception</strong>: to avoid long builds, the rebuild and force-build commands apply only
to the packages given on the command line. E.g., autoproj rebuild orocos/rtt
will only rebuild the orocos/rtt packages. If you want to rebuild both the
packages and its dependencies, use the --with-depends options.</p>

<p><strong>Generating documentation:</strong></p>

<p>Documentation is generated only when asked explicitly:</p>

<p class="commandline">autoproj doc</p>

<p>It generates documentation of packages that have some, and copies that
documentation into build/doc/, following the same layout than the source
directory.</p>

<p>All these commands (i.e. build, doc, and update) accept a package name as
argument, thus triggering build only for this package and its dependencies.</p>

<h2 id="shell_helpers">Shell helpers</h2>
<p>On bash and zsh, autoproj installs some shell helpers that make using an
autoproj installation easier.</p>

<p>For now, only the &ldquo;acd&rdquo; command is available.</p>

<pre><code>acd drivers/orogen/xsens_imu
</code></pre>

<p>will go in the directory where the &ldquo;drivers/orogen/xsens_imu&rdquo; package source is.
Package names can be shortened as long as there is no ambiguity. You can only
keep the prefixes at each directory level. For instance:</p>

<pre><code> acd xsens_imu
</code></pre>

<p>will fail as it matches drivers/xsens_imu and drivers/orogen/xsens_imu. However,</p>

<pre><code> acd o/x
</code></pre>

<p>will work, as well as d/o/xsens (for instance).</p>

<p><strong>Changing your configuration:</strong></p>

<p>After having boostrapped your Rock installation, autoproj will <em>not</em> ask you again about the configuration, since
the questions you already answered. So if you want to change your configuration, i.e. you answers do:</p>

<pre><code>autoproj build --reconfigure
</code></pre>

<p>Alternatively, you can edit autoproj/config.yml directly.</p>

<p>By default, autoproj does not automatically update the package after an reconfiguration. To do
that, you have to explicitly update your packages by calling. </p>

<p class="commandline">autoproj update</p>

<h2 id="switching-configuration">Switching configuration</h2>
<p>Let&rsquo;s assume that you want to switch what configuration autoproj is tracking,
but without having to redo a complete bootstrap &ndash; i.e. avoiding to rebuild
stuff that is common between the configurations.</p>

<p>autoproj provides the switch-config command for that. This command takes the
same arguments than the bootstrap script, that is:</p>

<p class="cmdline">autoproj switch-config [vcs_type] [vcs_url] [vcs_options]</p>

<p>As a shortcut, one can omit vcs_type and vcs_url if they don&rsquo;t change.
This is especially useful when switching between branches:</p>

<p class="cmdline">autoproj switch-config branch=experimental</p>



</div>
</div>
</div>
