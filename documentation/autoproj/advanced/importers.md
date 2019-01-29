---
layout: page
title: Advanced
section: Build System
secondsection: Package import
---

<div class="content2">
<div class="content2-pagetitle">Package import</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>As <a href="creating_pkg_set.html">we already mentioned</a>, the source.yml file contains information
on where to import the source packages from. This information is included in the
version_control field of the source.yml, and is of the general form:</p>

<pre><code class="language-yaml">version_control:
   package_name:
       vcs_def
   package_name:
       vcs_def:
</code></pre>

<p>Autoproj follows the following rules to find the importer definition for a given
package:</p>

<ul>
 <li>it loads the information contained in the version_control section of the
package set that defines the considered package</li>
 <li>it will apply any modification that is contained in the overrides section by
the package sets listed <em>after</em> the aforementionned package set</li>
</ul>

<p>For both the version_control and overrides section, autoproj will match the
package&rsquo;s name with the package_name fields. It will consider every matching
block (i.e. every package_name that matches), overriding earlier setup by later
ones.</p>

<p>As an example, let&rsquo;s consider the following manifest:</p>

<pre><code class="language-ruby">package_sets:
   - tools:
       - orocos.toolchain
   - rock.toolchain
</code></pre>

<p>The rock.toolchain package uses its own repository for
the typelib library. So, while the orocos.toolchain has the following source.yml:</p>

<pre><code class="language-yaml">version_control:
   - .*:
       type: git
       url: git://github.com/orocos-toolchain/$PACKAGE.git
</code></pre>

<p>rock.toolchain has</p>

<pre><code class="language-yaml">overrides:
   - typelib:
       url: git://github.com/rock-core/$PACKAGE.git
</code></pre>

<p>Another common use for overrides is to change the branch, for instance:</p>

<pre><code class="language-yaml">overrides:
   - typelib:
       url: git://github.com/rock-core/$PACKAGE.git
   - rtt:
       branch: experimental
</code></pre>

<p>The remaining of this page will detail the various options that exist to import
a source package.</p>

<h2 id="common-importer-options">Common Importer Options</h2>
<p>All importers support the following options:</p>

<pre><code class="language-yaml">version_control:
   - .*:
       retry_count: 2
</code></pre>

<p>It specifies the number of time the importer should retry before giving up. This
is useful for remote servers (svn, git) that are not as reliable as one would
like.</p>

<h2 id="all_importers">Git</h2>

<p>The general setup for git imports is:</p>

<pre><code class="language-yaml">version_control:
   package_name:
       type: git
       url: repository_url_or_path
       branch: branch_name
       tag: tag_name # it is branch OR tag
</code></pre>

<p>Autoproj will maintain an &lsquo;autobuild&rsquo; remote on the checked out repository:
i.e., it will make sure that the URL of this remote is always linked to the
right URL from the config file, and will update the relevant branch on update
(beware: the other branches won&rsquo;t get updated).</p>

<p>Moreover, autoproj will make sure that updating your local repository always
resolves as a fast-forward (i.e., it will never create a merge)</p>

<h2 id="subversion">Subversion</h2>

<p>The general setup for subversion imports is:</p>

<pre><code class="language-yaml">version_control:
   package_name:
       type: svn
       url: repository_url_or_path
</code></pre>

<p class="warning">Note that the repository <em>must</em> be accessible without any password - the
import will fail if a password is needed.</p>

<h2 id="cvs">CVS</h2>

<p>The general setup for CVS imports is:</p>

<pre><code class="language-yaml">version_control:
   package_name:
       type: cvs
       url: cvs_root
       module: modulename
</code></pre>

<p class="warning">Note that the repository <em>must</em> be accessible without any password - the
import will fail if a password is needed.</p>

<p>In case a :pserver: is used, it must be quoted - YAML would interpret it
the wrong way otherwise:</p>

<pre><code class="language-yaml">version_control:
   package_name:
       type: cvs
       url: ":pserver:cvs@blabla:/"
       module: modulename
</code></pre>

<h2 id="tar-archives">Tar archives</h2>

<pre><code class="language-yaml">version_control:
   package_name:
       type: archive
       url: http://sourceforge.net/blablabla.tar.gz?option=value
       filename: blabla.tar.gz # Optional: if the file name cannot be inferred from the URL
       no_subdirectory: false # Optional. Set to true if there is not a leading directory in the archive
       update_cached_file: false # Optional. Set to false to disable automatic updates
</code></pre>

<p>The importer expects that there is a leading subdirectory in the archive, under
which all files. If that is not the case, i.e. if all files are in the root of
the archive, do not forget to set the no_subdirectory option.</p>

<p>Autoproj tries to guess what is the name of the downloaded file by extracting it
out of the URL. Sometimes, this does not work as the URL does not fit the
expected scheme &ndash; in these cases you will get a tar error on update. To
override this autodetection behaviour, set the filename option to the actual
downloaded file name.</p>

<p>By default, autoproj will check if the downloaded file has been updated on the
server, and will download it again if it is the case. If you are downloading
release tarballs, this is unneeded as the archive should not be updated. In that
case, set the update_cached_file option to false to save the time needed to
check for the update (can be long on, for instance, sourceforge). The source
will of course be updated if you change the URL (i.e. to download a new release
of the same software).</p>

<h2 id="patching-the-source-once-it-is-checked-outupdated">Patching the source once it is checked out/updated</h2>

<p>It is possible to apply patches after a given package (imported by any of the
importer types) has been checked out/updated. To do so, simply add the option
<tt>patches:</tt> to the importer configuration and list the patches which should
be applied:</p>

<pre><code class="language-yaml">version_control:
   package_name:
       type: archive
       url: http://sourceforge.net/blablabla
       patches:
           - $AUTOPROJ_SOURCE_DIR/blablabla-01.patch
           - $AUTOPROJ_SOURCE_DIR/blablabla-02.patch
</code></pre>

<p>Note that in the example above, the patch is saved in the package set&rsquo;s folder
(the value of AUTOPROJ_SOURCE_DIR). This is a highly required practice.</p>

<p>Note that if the patch list changes (i.e. the <em>names</em> change), autoproj will
automatically unpatch and repatch as required. It is therefore highly required
to change the patch name if the patch changes.</p>

<p>The provided patches are by default applied with a patch level of 0 (passed to
patch through the -p option). This can be overriden on a patch-per-patch basis
by making the patch name an array as well:</p>

<pre><code class="language-yaml">version_control:
   package_name:
       type: archive
       url: http://sourceforge.net/blablabla
       patches:
           - [$AUTOPROJ_SOURCE_DIR/blablabla-01.patch, 1]
           - $AUTOPROJ_SOURCE_DIR/blablabla-02.patch
</code></pre>



</div>
</div>
</div>
