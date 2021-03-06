---
layout: page
title: Advanced
section: Build System
secondsection: Cross-Set Dependencies
---
<div class="content2">
<div class="content2-pagetitle">Cross-Set Dependencies</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>A given package set can tell autoproj to import another one BEFORE it gets
loaded. This is done in the &lsquo;imports&rsquo; section of the source.yml file, using the
same syntax that in the &lsquo;package_sets&rsquo; section of the manifest</p>

<p>For instance, the rock.core package set has</p>

<pre><code class="language-yaml">imports:
- github: orocos-toolchain/autoproj
</code></pre>

<p>which means that autoproj will automatically import the orocos toolchain build
configuration when the main manifest lists rock.core. It tells you so at
checkout with</p>

<pre>
autoproj: updating remote definitions of package sets
checking out git:git://github.com/rock-core/package_set branch=master
rock.core: auto-importing git:git://github.com/orocos-toolchain/autoproj
checking out git:git://github.com/orocos-toolchain/autoproj
</pre>

<p>and at update time with</p>

<pre>
autoproj: updating remote definitions of package sets
updating rock.core
rock.core: auto-importing orocos.toolchain
updating orocos.toolchain
</pre>

<p>If some imports are not needed (or harmful) to your complete build
configuration, they can be disabled package-set-wide either by modifying the
manifest or programmatically</p>

<p>Imports will be disabled if you add the &ldquo;auto_imports: false&rdquo; flag in the manifest
to the package set&rsquo;s definition:</p>

<pre><code class="language-yaml">package_sets:
- type: git
  url: git://github.com/rock-core/package_set
  auto_imports: false
</code></pre>

<p>Or by adding the following statement to autoproj/init.rb:</p>

<pre><code class="language-ruby">disable_imports_from 'rock.core'
</code></pre>



</div>
</div>
</div>
