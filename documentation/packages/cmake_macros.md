---
layout: page
hero_text: about Page!
title: Rock CMake Macros
section: Managing Packages
---
<div class="content2">
<div class="content2-pagetitle">Rock CMake Macros</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="initialization">Initialization</h2>

<p>Initialization must be done at the top of the toplevel CMakeLists.txt file. It
is done with:</p>

<pre><code class="language-text">cmake_minimum_required(VERSION 2.6)
find_package(Rock)
rock_init(dummyproject 0.1)
rock_standard_layout()
</code></pre>

<p>The signature of rock_init is:</p>

<pre><code>rock_init(project_name project_version)
</code></pre>

<p>The <code>rock_standard_layout()</code> call looks for sub-directories of the
standard C++ package layout and sets it up accordingly. See
<a href="http://rock.opendfki.de/wiki/WikiStart/Standards/RG4">the Rock guidelines</a> for
information about the standard package directory structure</p>

<h2 id="executable-targets-ttrockexecutablett">Executable Targets (<tt>rock_executable</tt>)</h2>

<pre>
rock_executable(name
    SOURCES source.cpp source1.cpp ...
    [DEPS target1 target2 target3]
    [DEPS_PKGCONFIG pkg1 pkg2 pkg3]
    [DEPS_CMAKE pkg1 pkg2 pkg3]
    [MOC qtsource1.hpp qtsource2.hpp])
</pre>

<p>Creates a C++ executable and (optionally) installs it.</p>

<p>The following arguments are mandatory:</p>

<p><strong>SOURCES</strong>: list of the C++ sources that should be built into that library</p>

<p>The following optional arguments are available:</p>

<p><strong>DEPS</strong>: lists the other targets from this CMake project against which the
library should be linked</p>

<p><strong>DEPS_PKGCONFIG</strong>: list of pkg-config packages that the library depends upon. The
necessary link and compilation flags are added</p>

<p><strong>DEPS_CMAKE</strong>: list of packages which can be found with CMake&rsquo;s find_package,
that the library depends upon. It is assumed that the Find*.cmake scripts
follow the CMake accepted standard for variable naming</p>

<p><strong>MOC</strong>: if the library is Qt-based, this is a list of either source or header
files of classes that need to be passed through Qt&rsquo;s moc compiler.  If headers
are listed, these headers should be processed by moc, with the resulting
implementation files are built into the library. If they are source files, they
get added to the library and the corresponding header file is passed to moc.</p>

<h2 id="library-targets-ttrocklibrarytt">Library Targets (<tt>rock_library</tt>)</h2>

<pre>
rock_library(name
    SOURCES source.cpp source1.cpp ...
    [DEPS target1 target2 target3]
    [DEPS_PKGCONFIG pkg1 pkg2 pkg3]
    [DEPS_CMAKE pkg1 pkg2 pkg3]
    [HEADERS header1.hpp header2.hpp header3.hpp ...]
    [MOC qtsource1.hpp qtsource2.hpp]
    [NOINSTALL])
</pre>

<p>Creates and (optionally) installs a shared library.</p>

<p>As with all rock libraries, the target must have a pkg-config file along, that
gets generated and (optionally) installed by the macro. The pkg-config file
needs to be in the same directory and called package_name.pc.in</p>

<p>The following arguments are mandatory:</p>

<p><strong>SOURCES</strong>: list of the C++ sources that should be built into that library</p>

<p>The following optional arguments are available:</p>

<p><strong>DEPS</strong>: lists the other targets from this CMake project against which the
library should be linked</p>

<p><strong>DEPS_PKGCONFIG</strong>: list of pkg-config packages that the library depends upon. The
necessary link and compilation flags are added</p>

<p><strong>DEPS_CMAKE</strong>: list of packages which can be found with CMake&rsquo;s find_package,
that the library depends upon. It is assumed that the Find*.cmake scripts
follow the CMake accepted standard for variable naming</p>

<p><strong>HEADERS</strong>: a list of headers that should be installed with the library. They get
installed in include/project_name</p>

<p><strong>MOC</strong>: if the library is Qt-based, this is a list of either source or header
files of classes that need to be passed through Qt&rsquo;s moc compiler.  If headers
are listed, these headers should be processed by moc, with the resulting
implementation files are built into the library. If they are source files, they
get added to the library and the corresponding header file is passed to moc.</p>

<p><strong>NOINSTALL</strong>: by default, the library gets installed on &lsquo;make install&rsquo;. If this
argument is given, this is turned off</p>

<h2 id="vizkit-plugin-targets-ttrockvizkitplugintt">Vizkit Plugin Targets (<tt>rock_vizkit_plugin</tt>)</h2>

<pre>
rock_vizkit_plugin(name
    SOURCES source.cpp source1.cpp ...
    [DEPS target1 target2 target3]
    [DEPS_PKGCONFIG pkg1 pkg2 pkg3]
    [DEPS_CMAKE pkg1 pkg2 pkg3]
    [HEADERS header1.hpp header2.hpp header3.hpp ...]
    [MOC qtsource1.hpp qtsource2.hpp]
    [NOINSTALL])
</pre>

<p>Creates and (optionally) installs a shared library that defines a vizkit
plugin. In Rock, vizkit is the base for data display. Vizkit plugins are
plugins to the 3D display in vizkit.</p>

<p>The library gets linked against the vizkit libraries automatically (no
need to list them in DEPS_PKGCONFIG). Moreover, unlike with a normal shared
library, the headers get installed in include/vizkit</p>

<p>The following arguments are mandatory:</p>

<p><strong>SOURCES</strong>: list of the C++ sources that should be built into that library</p>

<p>The following optional arguments are available:</p>

<p><strong>DEPS</strong>: lists the other targets from this CMake project against which the
library should be linked</p>

<p><strong>DEPS_PKGCONFIG</strong>: list of pkg-config packages that the library depends upon. The
necessary link and compilation flags are added</p>

<p><strong>DEPS_CMAKE</strong>: list of packages which can be found with CMake&rsquo;s find_package,
that the library depends upon. It is assumed that the Find*.cmake scripts
follow the CMake accepted standard for variable naming</p>

<p><strong>HEADERS</strong>: a list of headers that should be installed with the library. They get
installed in include/project_name</p>

<p><strong>MOC</strong>: if the library is Qt-based, this is a list of either source or header
files of classes that need to be passed through Qt&rsquo;s moc compiler.  If headers
are listed, these headers should be processed by moc, with the resulting
implementation files are built into the library. If they are source files, they
get added to the library and the corresponding header file is passed to moc.</p>

<p><strong>NOINSTALL</strong>: by default, the library gets installed on &lsquo;make install&rsquo;. If this
argument is given, this is turned off</p>

<h2 id="vizkit-widget-targets-ttrockvizkitwidgettt">Vizkit Widget Targets (<tt>rock_vizkit_widget</tt>)</h2>

<pre>
rock_vizkit_widget(name
    SOURCES source.cpp source1.cpp ...
    [DEPS target1 target2 target3]
    [DEPS_PKGCONFIG pkg1 pkg2 pkg3]
    [DEPS_CMAKE pkg1 pkg2 pkg3]
    [HEADERS header1.hpp header2.hpp header3.hpp ...]
    [MOC qtsource1.hpp qtsource2.hpp]
    [NOINSTALL])
</pre>

<p>Creates and (optionally) installs a shared library that defines a vizkit
widget. In Rock, vizkit is the base for data display. Vizkit widgets are
Qt designer widgets that can be seamlessly integrated in the vizkit framework.</p>

<p>If a file exists that goes by the name package_name.rb exists, it is assumed to be
a ruby extension used to extend the C++ interface in ruby scripting. It gets
installed in share/vizkit/cplusplus_extensions, where vizkit is looking for
it.</p>

<p>The library gets linked against the QtCore library automatically (no
need to list them in DEPS_PKGCONFIG). Moreover, unlike with a normal shared
library, the headers get installed in include/package_name</p>

<p>The following arguments are mandatory:</p>

<p><strong>SOURCES</strong>: list of the C++ sources that should be built into that library</p>

<p><strong>MOC</strong>: if the library is Qt-based, this is a list of either source or header
files of classes that need to be passed through Qt&rsquo;s moc compiler.  If headers
are listed, these headers should be processed by moc, with the resulting
implementation files are built into the library. If they are source files, they
get added to the library and the corresponding header file is passed to moc.</p>

<p>The following optional arguments are available:</p>

<p><strong>DEPS</strong>: lists the other targets from this CMake project against which the
library should be linked</p>

<p><strong>DEPS_PKGCONFIG</strong>: list of pkg-config packages that the library depends upon. The
necessary link and compilation flags are added</p>

<p><strong>DEPS_CMAKE</strong>: list of packages which can be found with CMake&rsquo;s find_package,
that the library depends upon. It is assumed that the Find*.cmake scripts
follow the CMake accepted standard for variable naming</p>

<p><strong>HEADERS</strong>: a list of headers that should be installed with the library. They get
installed in include/project_name</p>

<p><strong>NOINSTALL</strong>: by default, the library gets installed on &lsquo;make install&rsquo;. If this
argument is given, this is turned off</p>

<h2 id="test-suite-targets-ttrocktestsuitett">Test Suite Targets (<tt>rock_testsuite</tt>)</h2>

<pre>
rock_testsuite(name
    SOURCES source.cpp source1.cpp ...
    [DEPS target1 target2 target3]
    [DEPS_PKGCONFIG pkg1 pkg2 pkg3]
    [DEPS_CMAKE pkg1 pkg2 pkg3]
    [MOC qtsource1.hpp qtsource2.hpp])
</pre>

<p>Creates a C++ test suite that is using the boost unit test framework</p>

<p>The following arguments are mandatory:</p>

<p><strong>SOURCES</strong>: list of the C++ sources that should be built into that library</p>

<p>The following optional arguments are available:</p>

<p><strong>DEPS</strong>: lists the other targets from this CMake project against which the
library should be linked</p>

<p><strong>DEPS_PKGCONFIG</strong>: list of pkg-config packages that the library depends upon. The
necessary link and compilation flags are added</p>

<p><strong>DEPS_CMAKE</strong>: list of packages which can be found with CMake&rsquo;s find_package,
that the library depends upon. It is assumed that the Find*.cmake scripts
follow the CMake accepted standard for variable naming</p>

<p><strong>MOC</strong>: if the library is Qt-based, this is a list of either source or header
files of classes that need to be passed through Qt&rsquo;s moc compiler.  If headers
are listed, these headers should be processed by moc, with the resulting
implementation files are built into the library. If they are source files, they
get added to the library and the corresponding header file is passed to moc.</p>



</div>
</div>
</div>
