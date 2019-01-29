---
layout: page
title: Type definitions
section: Developing Components
---
<div class="content2">
<div class="content2-pagetitle">Type definitions</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>One of the first thing that a component designer has to think about is defining
the data structures that will be used in the component&rsquo;s interfaces:</p>

<ul>
  <li>in the communication between components (ports)</li>
  <li>in the configuration of the component (properties)</li>
  <li>in the control of the component (operations)</li>
</ul>

<p>From your component definitions, oroGen will generate the necessary RTT plugins
to support:</p>

<ul>
  <li>XML marshalling as .xml or .cpf</li>
  <li>CORBA marshalling to use with the CORBA transport</li>
  <li>Typelib marshalling to use with the data logger</li>
  <li>the classes needed for the Ruby bindings</li>
</ul>

<p>In principle, if you are using oroGen, all the tools that are offered by the
Rock toolchain should work seamlessly. If they don&rsquo;t, it&rsquo;s because you
found a bug !</p>

<p>The type-handling part of oroGen is available as a standalone tool for people
that want to develop RTT components directly. This tools is called typeGen and
presented at the end of this page.</p>

<h2 id="defining-types">Defining types</h2>
<p>In oroGen, the types are described in C++. However, not all C++ types can be
used in the data flow. To be usable in the data flow, a type must:</p>

<ul>
  <li>be default constructible and copyable (i.e. have a constructor that have no
arguments and can be copied).</li>
  <li>have no private fields</li>
  <li>have no parent class (this might be removed in a future version of oroGen)</li>
</ul>

<p>Moreover, oroGen has a special support for the std::string and std::vector
standard classes, so you can use them freely.</p>

<p>Example: defining a Time class</p>

<pre><code class="language-cpp">namespace base {
  struct Time
  {
    uint64_t microseconds;
    static Time fromMilliseconds(uint64_t ms);
    Time operator +(Time const&amp; other);
  };
}
</code></pre>

<p>Note that, for now, all types that are defined in the orogen project must be
defined inline: orogen does not support separating the implementation from the
definition. Types that are defined in separate libraries are fine, though.</p>

<h2 id="known-limitations">Known Limitations</h2>

<ul>
  <li>structures and classes with private members are not handled (a warning is
issued)</li>
  <li>structures and classes that have a parent class are not handled (a warning is
issued)</li>
  <li>char, short, 64 bit integers and float are forbidden as argument types for an
operation. Use int instead of char or short, and double instead of
float. Unfortunately there is no equivalent for 64 bit integers. This is due
to limitations in the RTT default typekit.</li>
  <li>structs that contain 64 bit integers won&rsquo;t be marshallable as XML. This is
because the CPF file format does not have 64bit integer support either.</li>
  <li>there is no typelib marshalling - and hence no logging - for base types (int,
float, &hellip;). They work fine if used as fields in a structure though.</li>
</ul>

<h2 id="what-to-do-if-your-type-cannot-be-understood-by-orogen-">What to do if your type cannot be understood by oroGen ?</h2>
<p>For types that can&rsquo;t be directly managed by oroGen, a special mechanism allows
to give a way for oroGen to manipulate the data. This is unfortunately only
available to the users of oroGen &ndash; not to the users of typeGen &ndash; for now. See the <a href="opaque_types.html">opaque
types</a> section for more information.</p>

<h2 id="defining-types-in-an-orogen-project">Defining types in an oroGen project</h2>
<p>In an oroGen project, one adds one or many following statement to load a C/C++
file that defines the project&rsquo;s types:</p>

<pre><code class="language-ruby">import_types_from "myproject.h"
</code></pre>

<p>Be aware that <tt>myproject.h</tt> must be self-consistent, i.e. that it must
include all the headers that are necessary to define the types that it wants to
define.</p>

<p>Moreover, only the types that are <em>directly</em> defined in myproject.h will be
exported in the typekit. It means that the include order actually matters
(that&rsquo;s a bug, not a feature).</p>

<p>In other words, if <tt>other_header.h</tt> is included in <tt>myproject.h</tt>
<strong>and</strong> you want the types of <tt>other_header.h</tt> to be exported in the
typekit, then you need to do</p>

<pre><code class="language-ruby">import_types_from "other_header.h"
import_types_from "myproject.h"
</code></pre>

<p>and not</p>

<pre><code class="language-ruby">import_types_from "myproject.h"
import_types_from "other_header.h"
</code></pre>

<p>Finally, one can directly use types defined in a library, provided that this
library gives a pkg-config file for dependency discovery.</p>

<p>Let&rsquo;s consider a &lsquo;drivers/hokuyo&rsquo; package that would define a hokuyo::Statistics
structure. Assuming that this package (1) installs a hokuyo.pc file, and (2)
installs the relevant header as &ldquo;hokuyo.hpp&rdquo;, it is possible to do</p>

<pre><code class="language-ruby">using_library "hokuyo"
import_types_from "hokuyo.hpp"
</code></pre>

<p>The issue of loading types from other oroGen projects is handled <a href="cross_project.html">in
here</a></p>

<h2 id="the-special-case-of-templates">The special case of templates</h2>
<p>Templates are not directly understood by oroGen. Howver, explicit instanciations
of them can be used.</p>

<p>Unfortunately, typedef&rsquo;ing the type that you need is not enough for gccxml &ndash;
the tool that parses C++ for oroGen. You have to use the instanciated template
directly in a structure. To work around this, you can define a structure whose
name contains gccxml_workaround to get the template instanciated, and then
define the typedefs that you will actually use in your typekits and oroGen task
interfaces.</p>

<p>For instance, with</p>

<pre><code class="language-cpp">template &lt;typename Scalar, int DIM&gt;
struct Vector {
  Scalar values[DIM];
};

struct __orogen_workaround {
  Vector&lt;3&gt; vector3;
  Vector&lt;4&gt; vector4;
};
</code></pre>

<p>one can use Vector&lt;3&gt; in its orogen interface, and in other structures.</p>

<h2 id="standalone-typekits">Standalone typekits</h2>

<p>If you don&rsquo;t want to use oroGen. The orogen package offers the typegen tool to
generate the typekits for people that want to write their components themselves.</p>

<p>To generate a typekit using typegen, do the following:</p>

<ul>
  <li>write your types as described in this page</li>
  <li>save all of them under the same subdirectory, and put only these types there</li>
  <li>
    <p>run</p>

    <p class="commandline">typegen <em>typekit_name</em> <em>inputs</em></p>

    <p>where <em>inputs</em> is the input directory.</p>
  </li>
</ul>

<p>typeGen will generate a typekit/ directory which includes both the typekit code and
the cmake code that is needed to compile it.</p>

<p>All the caveats described in the oroGen section apply:</p>

<ul>
  <li>
    <p>import order might matter. If you are in a situation where it does matter, you
will have to list the header files one by one on the command line. Note that
it is fine to do</p>

    <p>typegen <em>typekit_name</em> <em>header_file</em> <em>input_directory</em></p>

    <p>to ensure that <em>header_file</em> will be loaded before all the other files in the
directory.</p>
  </li>
  <li>
    <p>it is possible to load headers from libraries that define pkg-config files and
from other typegen/orogen project. Use the <tt>-i</tt> option instead of using
using_library and import_types_from.</p>
  </li>
</ul>



</div>
</div>
</div>
