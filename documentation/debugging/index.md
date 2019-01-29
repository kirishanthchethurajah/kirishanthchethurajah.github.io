---
layout: page
title: Introduction
section: Debugging Techniques
---
<div class="content2">
<div class="content2-pagetitle">Base Logging</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="introduction">Introduction</h2>

<p>Rock provides a logger that facilitates and unifies debugging of system libraries.
The following section describes the functionality of the base logger and how to use it.</p>

<p>For debugging of running/existing components please refer to <a href="../runtime/debugging.html">Debugging Components</a>
from the Core/Running Components section.</p>

<h2 id="adding-the-base-logger-to-a-library">Adding the base logger to a library</h2>

<p>When using the <a href="../packages/cmake_macros.html">Rock CMake Macros</a>, integration of the base logger is fairly easy.
All you need to do is adding <em>base-lib</em> to your list of package config dependencies and
include the header <em>base/logging.h</em> where logging is needed. </p>

<p>When using Rock CMake Macros add:</p>
<pre>
rock_library(test_library
         DEPS_PKGCONFIG base-lib
         ...)
</pre>

<p>When <strong>not</strong> using Rock CMake Macros you have to set the logging
namespace and embed the base library:</p>

<pre>
...
add_definitions(-DBASE_LOG_NAMESPACE=${PROJECT_NAME})

include(FindPkgConfig)

pkg_check_modules(BASE REQUIRED "base-lib")
include_directories(${BASE_INCLUDE_DIRS})
link_directories(${BASE_LIBRARY_DIRS})

add_library(your_library ...)
target_link_libraries(your_library ... ${BASE_LIBRARIES})
</pre>

<h2 id="using-the-logger">Using the logger</h2>
<p>The logger allows you to log either in a printf-style or stream-style.
For that purpose include it where needed and preferably in the .cpp files to avoid warnings about an undefined namespace for the logging.</p>

<pre><code class="language-cpp">#include&lt;base/logging.h&gt;

...
// printf-style logging
std::string information("test-logging happening here");
LOG_DEBUG("I provide you the following debug information: %s",
    information.c_str());
LOG_INFO(...);
LOG_WARN(...);
LOG_ERROR(..);
LOG_FATAL(..);

// stream-style logging
LOG_DEBUG_S &lt;&lt; information;
LOG_INFO_S &lt;&lt; information;
...
LOG_FATAL &lt;&lt; information;
</code></pre>

<p>The default output format of a log message looks like the following (without linebreak):</p>

<pre><code class="language-sh">[20120120-11:58:00:577] [FATAL] - test_logger::A fatal error
occurred (/tmp/test_logger/src/Main.cpp:10 - int main(int, char**))
</code></pre>

<h2 id="configuring-the-base-logger">Configuring the base logger</h2>
<p>The logger can be configured at compile time to remove unwanted logging statement in a release version, and at runtime to control the verbosity level and logging format.</p>

<h3 id="compile-time">Compile-time</h3>
<p>The following <strong><em>defines</em></strong> can be set to configure the logger at compile time: </p>

<p><strong>BASE_LOG_NAMESPACE</strong> Setting the namespace to a unique string that allows you to filter the logging output, e.g. ROCK Macros automatically set it to the package name</p>

<p><strong>BASE_LOG_DISABLE</strong> Deactivate all logging statements, e.g. for a realease binary.</p>

<p><strong>BASE_LOG_xxx</strong> Compile only logs equal or higher than a certain level xxx into your binary, i.e. BASE_LOG_WARN disable all logs except for WARN, ERROR and FATAL
 Settings of log levels via environment variables with then only apply to the still activated levels. </p>

<p><strong>BASE_LONG_NAMES</strong> Set this define in order to enforce a BASE_ prefix for the logging Macros, to avoid clashes with other libraries, i.e. BASE_LOG_DEBUG(&hellip;)</p>

<h3 id="at-run-time">At run-time</h3>
<p>The following <strong><em>enviroment variables</em></strong> can be used to control the behaviour of the logger: </p>

<p><strong>BASE_LOG_LEVEL</strong> Set to one of DEBUG, INFO, WARN, ERROR or FATAL to define the maximum logging level, e.g. export BASE_LOG_LEVEL=&rdquo;DEBUG&rdquo;</p>

<p><strong>BASE_LOG_COLOR</strong> Activate colored logging (current configuration best for dark background coloured terminals), e.g. export BASE_LOG_COLOR</p>

<p><strong>BASE_LOG_FORMAT</strong> Select from a set of predefined logging formats: DEFAULT, MULTILINE, SHORT, example outputs are given below:</p>

<p>The DEFAULT format contains no linebreaks (here only due to fit the narrow presentation):</p>

<pre><code class="language-sh">[20120120-11:58:00:577] [FATAL] - test_logger::A fatal error
occurred (/tmp/test_logger/src/Main.cpp:10 - int main(int, char**))
</code></pre>

<p>The SHORT format reduces information to the log priority and the message:</p>

<pre><code class="language-sh">[FATAL] - test_logger::A fatal error occurred
</code></pre>

<p>The MULTILINE format split each individual log message across a minimum of three lines:</p>

<pre><code class="language-sh">[20120120-11:58:59:976] in int main(int, char**)
        /tmp/test_logger/src/Main.cpp:10
        [FATAL] - test_logger::A fatal error occurred
</code></pre>



</div>
</div>
</div>
