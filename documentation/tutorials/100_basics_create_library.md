---
layout: page
title: Creating libraries
section: Basics
---

<div class="content2">
<div class="content2-pagetitle">Creating libraries</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="abstract">Abstract</h2>

<p>This tutorial will give you some hands-on experience on:</p>

<ul>
  <li>How to create libraries in Rock and</li>
  <li>how to embed them into the build system of Rock.</li>
</ul>

<p>If you don&rsquo;t want to execute the following steps yourself, the result can
also be found in the package &lsquo;tutorials/&rsquo; after you <a href="index.html#installing">installed the tutorial
package set</a>.</p>

<p>For this tutorial, it is assumed that your autoproj installation can be found in
~/dev.</p>

<h2 id="creating-libraries">Creating libraries</h2>
<p>Before you start developing components, you will need to think about the
functionality that is required for your component. This tutorial teaches you
how to write a message producer and a message consumer component, which will
pass timestamped messages between each other.  For the message producer and
the consumer, we will create a message library to provide message creation and
message printing functionality. This library will make use of the existing
package base/types, which simplifies creation of timestamps.</p>

<p>Now, that it is clear what functionality is needed, you can start writing the
library.  Note, that Rock strongly suggests to encapsulate your main
functionality in libraries. Thus, your library code remains independent of the
actual framework in use, and can be easily reused and maintained separately
from any framework that wraps this functionality.</p>

<p>Rock allows you to create a C++ library from an existing template. Calling the
command &lsquo;rock-create-lib&rsquo; starts a command line dialog to create a library
called &lsquo;tutorials/message_driver&rsquo;. Remember: ~/dev is the path to the Rock
installation. It might differ on your machine.</p>

<pre><code class="language-text">~/dev$ rock-create-lib tutorials/message_driver
</code></pre>

<p>Once the the create script is called, a command line dialog is started which will
request basic information to configure the template for you. The following
output is the output of the script AND the expected answers. Do not copy/paste
the whole block at once !</p>

<pre><code class="language-text">Initialized empty Git repository
in ~/dev/tutorials/message_driver/.git/
Do you want to start the configuration of
the cmake project: message_driver
Proceed [y|n]
y
------------------------------------------
We require some information to update the manifest.xml
------------------------------------------
Brief package description (Press ENTER when finished):
A message_driver for the basic Rock tutorial
Long description:
This is a library that allows message production
and message handling for the the basic Rock tutorial
Author:
New user
Author email:
new-user@rock-robotics.org
Url (optional):

Enter your dependencies as a comma separated list.
Press ENTER when finished:
base/types
Initialized empty shared Git repository
in ~/dev/tutorials/message_driver/.git/
[master (root-commit) 37aa552] Initial commit
8 files changed, 108 insertions(+), 0 deletions(-)
create mode 100644 CMakeLists.txt
create mode 100644 INSTALL
create mode 100644 LICENSE
create mode 100644 README
create mode 100644 manifest.xml
create mode 100644 src/CMakeLists.txt
create mode 100644 src/Dummy.cpp
create mode 100644 src/Dummy.hpp
create mode 100644 src/Main.cpp
create mode 100644 src/dummyproject.pc.in
Done.
</code></pre>

<p>The newly created package comes in some kind of ready-to-run: You can build and install it right away using the build tool <a href="../autoproj/basic_usage.html">autoproj</a>.</p>

<pre><code class="language-text">amake tutorials/message_driver
</code></pre>

<p class="warning"><strong><em>NOTE</em></strong>: The template project will generate several files for you in the src/ and
test/ directories. This is meant to give you an example, but these files are
usually deleted as soon as you start developing the library. Don&rsquo;t forget to
remove the corresponding references from src/CMakeLists.txt and
test/CMakeLists.txt as well.</p>

<h3 id="adding-the-required-functionality">Adding the required functionality</h3>
<p>The library does not contain message handling capabilities yet. So we create
three new files, i.e. a header file Messages.hpp which will contain the message
type that is used to transport message between components, and a header and
source file for the library functionality, the MessageDriver.
Put all those files into the src/ folder of the
newly created package (~/dev/tutorials/message_driver/src).</p>

<p class="warning"><strong><em>NOTE</em></strong>: Rock recommends to stick to use CamelCase for new structures, and to
name the files after the class it defines (i.e. Message.hpp defines the Message
structure, MessageDriver.hpp declares the MessageDriver class and
MessageDriver.cpp defines it). However, due to historic reasons, not all packages
of Rock conform to this style. See <a href="http://rock.opendfki.de/wiki/WikiStart/Standards/RG4">these guidelines</a>
for more information</p>

<p>In Rock, common C++ types are defined <a href="/pkg/base/types/index.html">in the base/types package</a>. If some type you need is defined here, it is <strong>highly
recommended</strong> to use the common type. In our case, we want to timestamp the
message that our library will manipulate.  If we have a look at the
<a href="../../api/base/types/structbase_1_1Time.html">base/types</a>) API documentation
we can see that there is a base::Time type that suits our needs.</p>

<p>First things first, we need to declare the dependency on the base/types package.
Edit manifest.xml and make sure that the following line is there (it should already be there):</p>

<pre><code class="language-xml">&lt;depend package="base/types" /&gt;
</code></pre>

<p>The message class definition will be contained in a header called
src/Message.hpp, to follow Rock naming guidelines (a Message class should be
declared in the src/Message.hpp header). It should contain the following code:</p>

<pre><code class="language-cpp">#ifndef _MESSAGE_DRIVER_MESSAGE_HPP_
#define _MESSAGE_DRIVER_MESSAGE_HPP_

#include &lt;string&gt;
#include &lt;base/Time.hpp&gt;

namespace message_driver
{
    struct Message
    {
        // The message content
        std::string content;

        // The timestamp when the message was created
        base::Time time;

	// Default Constructor -- required
        Message()
                : content()
                , time(base::Time::now())
        {
        }

        Message(const std::string&amp; msg)
                : content(msg)
                , time(base::Time::now())
        {
        }
    };

}
#endif // _MESSAGE_DRIVER_MESSAGE_HPP_
</code></pre>

<p>To follow the Rock guidelines, the MessageDriver class should be declared in
src/MessageDriver.hpp and defined in src/MessageDriver.cpp.
src/MessageDriver.hpp should therefore contain:</p>

<pre><code class="language-cpp">#ifndef _MESSAGE_DRIVER_HPP_
#define _MESSAGE_DRIVER_HPP_

#include &lt;message_driver/Message.hpp&gt;

namespace message_driver
{

class MessageDriver
{

public:
    /**
     * Create a timestamped message
     * \return A timestamped message
     */
    Message createMessage();

    /**
     * Print a message to stdout
     * \param msg Message to be printed
     */
    void printMessage(const Message&amp; msg);
};

}

#endif // _MESSAGE_DRIVER_HPP_
</code></pre>

<p>And, finally, the driver implementation creates the timestamped message, and is
in src/MessageDriver.cpp:</p>

<pre><code class="language-cpp">#include "MessageDriver.hpp"
#include &lt;iostream&gt;

namespace message_driver
{

Message MessageDriver::createMessage()
{
        Message msg("Message from MessageDriver");
        return msg;
}

void MessageDriver::printMessage(const Message&amp; msg)
{
        std::cout &lt;&lt; "[" &lt;&lt; msg.time.toString()
                  &lt;&lt; "] " &lt;&lt; msg.content
                  &lt;&lt; std::endl;
}

}
</code></pre>

<h3 id="build">Build</h3>

<p>Once you created a library in Rock, integrate it into the build system autoproj.
The first step towards integration into the build system is adding the newly
created files to the src/CMakeLists.txt file, since Rock C++ libraries
use CMake for the build process by default. You can use standard CMake directives, but Rock
also comes with some CMake macros that facilitate setting up libraries, and
resolving any required dependencies (have a look <a href="../packages/cmake_macros.html">here</a> for details).</p>

<pre><code class="language-text">rock_library(message_driver
    SOURCES MessageDriver.cpp
    HEADERS MessageDriver.hpp Message.hpp
    DEPS_PKGCONFIG base-types)

# Do not forget to remove the rock_executable line that
# compiles Main.cpp, as it is not used anymore
</code></pre>

<p id="add-to-manifest">After adapting the CMakeLists.txt, add the package to the build configuration, so
that eventually you can embed the library into oroGen components. The easiest
way to adapt the build configuration is by adding the package to the manifest&rsquo;s
layout section. Thus, edit ~/dev/autoproj/manifest and add the package to the
layout section. Package management in detail is discussed in <a href="../autoproj/adding_packages.html">Adding
packages</a>.</p>

<p class="warning"><strong><em>NOTE</em></strong>: When adding the package, make sure you use the same indentation as
the previous line, here &lsquo;- rock.toolchain&rsquo;. The manifest file is parsed as
.yaml, and thus relies on proper indentation.</p>

<pre><code class="language-text">package_sets:
  - github: rock-core/package_set

# Layout. Note that the rock.base, rock.toolchain
# and orocos.toolchain sets are imported
# by other rock sets.
layout:
   - rock.core
   - tutorials/message_driver
</code></pre>

<h3 id="build-it">Build it</h3>
<p>Just verify that your component builds.</p>

<pre><code class="language-text">~/dev/tutorials/message_driver$ amake
</code></pre>

<h3 id="test-it">Test it</h3>
<p>Optionally, you can modify the Boost test_suite created by default and test our MessageDriver library.
So we create a new test file, i.e.: test/test_MessageDriver.cpp which will create and print a Message:</p>

<pre><code class="language-cpp">#include &lt;boost/test/unit_test.hpp&gt;
#include &lt;message_driver/MessageDriver.hpp&gt;

using namespace message_driver;

BOOST_AUTO_TEST_CASE(it_should_not_crash_when_message_is_created)
{
    message_driver::MessageDriver messageDriver;

    Message message = messageDriver.createMessage();

    messageDriver.printMessage(message);
}
</code></pre>

<p>In the same manner as for building the library, the step towards integration into the build system is adding the newly
created file to the test/CMakeLists.txt file</p>

<pre><code class="language-text">rock_testsuite(test_suite suite.cpp
    test_MessageDriver.cpp
    DEPS message_driver)
</code></pre>

<p>After adapting the CMakeLists.txt for the test_suite we can just build the library again and run the test_suite.</p>

<pre><code class="language-text">~/dev/tutorials/message_driver$ amake
~/dev/tutorials/message_driver$ ./build/test/test_suite
Running 1 test case...
[20140123-16:20:14:704568] Message from MessageDriver

*** No errors detected
</code></pre>

<p>You already finished your first Rock tutorial.</p>

<h2 id="summary">Summary</h2>
<p>In this tutorial you have learned to: </p>

<ul>
  <li>Create a C++ library from the Rock template and</li>
  <li>embed new packages into the build system.</li>
</ul>

<p>In the next tutorial you will learn how to create an oroGen component and embed your library into it.</p>

<p>Progress to the <a href="110_basics_create_component.html">next tutorial</a>.</p>


</div>
</div>
</div>
