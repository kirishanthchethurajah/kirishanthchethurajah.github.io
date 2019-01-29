---
layout: page
title: Advanced - Opaque types
section: Developing Components
---
<div class="content2">
<div class="content2-pagetitle">Advanced - Opaque types</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p class="warning">The opaque type support is not yet available to the users of typeGen</p>

<h2 id="what-are-they-">What are they ?</h2>
<p>Opaque types are a way to enable oroGen to handle types that it cannot handle
completely automatically. The general idea is that you provide oroGen with a
&ldquo;marshalling structure&rdquo; that (1) <a href="type_definitions.html">it can understand</a> and
(2) can hold all the data that the &ldquo;real type&rdquo; holds. Then, you have to
implement two convertion functions: one that converts from the marshalling type,
and one to the marshalling type.</p>

<p>So, it involves doing one copy. What is the gain ?</p>

<p>Unlike handwritten typekits, another solution to this problem, opaque types
provide you with the advantage that other types that use opaque types (i.e.
structures with fields that are from opaque types, std::vector, arrays) will be
automatically handled by oroGen. I.e. you write the convertion function for the
types that oroGen can&rsquo;t handle and let it do the rest of the work.</p>

<p>Moreover, oroGen will be able to generate typekits for all the transports it can
handle.</p>

<p>Finally, the convertion to and from the marshalling type is only done in
transports. Thanks to RTT&rsquo;s use of plain C++ structures to implement its
dataflow, there is no need for convertions in intra-process dataflow.</p>

<h2 id="how-to-use-them">How to use them</h2>

<p>You first have to create a wrapper type (a.k.a. &ldquo;intermediate type&rdquo;) for the
opaque. In the case of Eigen::Vector3d, a suitable wrapper would be</p>

<pre><code class="language-cpp">namespace wrappers
{
 struct Vector3d
 {
     double x, y, z;
 };
}
</code></pre>

<p>This wrapper then needs to be loaded with #import_types_from. Finally, one can
use #opaque_type to declare the opaque.</p>

<pre><code class="language-ruby">import_types_from "wrappers/my_opaque_wrapper.h"
opaque_type '/Eigen/Vector3d", "/wrappers/Vector3d"
</code></pre>

<p>where wrappers::Vector3d is defined in the my_opaque_wrapper.h header. Moreover,
if getting the definition of the opaque type requires new include directories
that are not yet added to the typekit through the <a href="cross_project.html#using_library">using_library
mechanism</a>, you will have to detect them in
the Ruby code and add them with the :include option</p>

<pre><code class="language-ruby">import_types_from "wrappers/my_opaque_wrapper.h"
opaque_type '/Eigen/Vector3d", "/wrappers/Vector3d", :includes =&gt; eigen_prefix
</code></pre>

<p>Once you have re-generated the project, a typekit/ directory is created with
Opaques.cpp and Opaques.hpp in it. These files hold the convertion functions
that should be used by oroGen to convert the opaque to the wrapper and the
wrapper to the opaque.</p>

<p class="warning">As usual, if you add new opaques to an orogen project that already has some, you
will need to copy the new toIntermediate/fromIntermediate convertion functions
from templates/typekit/Opaques.cpp</p>

<h2 id="the-m-types-automatically-generated-intermediate-types">The m-types: automatically generated intermediate types</h2>
<p>As explained, once you have defined an opaque type, oroGen will take care of
other types that <em>use</em> this opaque. For instance</p>

<pre><code class="language-cpp">struct Position
{
 base::Time time;
 Eigen::Vector3d position;
};
</code></pre>

<p>can be used in your task interfaces without any modifications. This works for
structures, std::vector and static-size arrays.</p>



</div>
</div>
</div>
