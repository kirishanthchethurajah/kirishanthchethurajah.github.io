---
layout: page
title: Base Types
section: Base Types
---

<div class="content2">
<div class="content2-pagetitle">Base Types</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="introduction">Introduction</h2>

<p>The package <code>base/types</code> contains a collection of C++ types that are shared
between modules. The general policy is that if there is more than one library
that generates a certain datatype, that type should go into the <code>base/types</code>
library. In general, types in base should be <a href="orogen/type_definitions.html">compatible to
oroGen</a>, so that they can be used as interface
types between tasks. </p>

<p>Further, there is a distinction between basic shared types and types that
represent sensor samples. In general sensor information should always be
timestamped. The convention is that types in samples always have a <code>time</code>
member, which is of type <code>base::Time</code>. All types in base are in the <code>base</code>
namespace. Samples are in the <code>base::samples</code> namespace.</p>

<p>Having shared types is very important to maintain interoperability between
libraries. Whenever new types are generated, it should be considered if not one
of the existing types in base can be used, or related types in other packages
can be generalized and moved into base.</p>

<h2 id="basic-types">Basic Types</h2>

<p>Following is a list of basic types and how they should be used. For more
information please refer to the api docs of the <code>base/types</code> package.</p>

<h3 id="time">Time</h3>

<p><code>base::Time</code> is one of the most used types. It internally represents the time as
microseconds since UNIX epoch. It can be used to represent both absolute and
relative time. </p>

<pre><code class="language-cpp">#include &lt;base/time.h&gt;

// initialize time to 0
base::Time t0 = base::Time();
// get the current time
base::Time tnow = base::Time::now();
// get a time of 2.5 seconds
base::Time t1 = base::Time::fromSeconds( 2.5 );
// tpast would be the time 2.5 seconds ago
base::Time tpast = tnow - t1;
</code></pre>

<p>Use <code>toString()</code> to get a human readable form. Also, there is a stream operator
for the Time class.</p>

<h3 id="timemark">TimeMark</h3>

<p>Small convenience class to simplify benchmarking. It will record the time on
generation, and has a simple stream method for the output.</p>

<pre><code class="language-cpp">#include &lt;base/timemark.h&gt;
#include &lt;iostream&gt;

base::TimeMark mark("Complex Stuff");
// do complex stuff
...
std::cout &lt;&lt; mark &lt;&lt; std::endl;
</code></pre>

<h3 id="angle">Angle</h3>

<p>The <code>base::Angle</code> class represents an angle and can be used as a convenience
class instead of a float. An angle object has a canonical interpretation within
-M_PI and M_PI. It can also perform converting between radians and degrees.</p>

<pre><code class="language-cpp">#include &lt;base/angle.h&gt;

// storing angles and the canonical form
base::Angle a = base::Angle::fromDegree(540.0);
base::Angle b = base::Angle::fromRad(M_PI);
a.isApprox( b ); // is true

// converting from degree to rad
needsRad( base::Angle::deg2Rad( 180.0 ) );
</code></pre>

<h3 id="matrix--vector">Matrix / Vector</h3>

<p>The base types use the <a href="http://eigen.tuxfamily.org">Eigen3</a> library for
handling and representing matrices and vectors. Eigen types have special
constraints regarding alignment, which require special template parameters to be
used for using Eigen types as interface types. To simplify this, <code>base</code> declares
a number of typedefs, which are equivalent to the corresponding Eigen types, but
having alignment switched off.</p>

<ul>
<li><code>base::Vector2d</code></li>
<li><code>base::Vector3d</code></li>
<li><code>base::Matrix2d</code></li>
<li><code>base::Matrix3d</code></li>
<li><code>base::Matrix4d</code></li>
<li><code>base::Quaterniond</code></li>
<li><code>base::Affine3d</code></li>
</ul>

<p>Also have a look at the
<a href="http://rock.opendfki.de/wiki/WikiStart/Toolchain/EigenTypes">wiki</a> page on the
subject. Also, for the eigen types, the <a href="orogen/opaque_types.html">opaque</a>
mechanism is used.</p>

<h3 id="pose">Pose</h3>

<p>For the base types, the <code>base::Pose</code> type is considered a type, which has a 3d
<code>orientation</code> and <code>position</code> member. The pose is used to represent the position
and orientation of the robot. There is also <code>base::Pose2D</code>, which is the
equivalent for the 2D case. Note, that both <code>base::Pose</code> and <code>base::Affine3d</code>
represent the same type of data. The Affine3d class is more verbose, but also
more effective to compute with. In general, the base::Pose should be used for
interfacing, while Affine3d is likely to be used in places where a lot of
transformations are involved.</p>

<h3 id="motion-command">Motion Command</h3>

<p>Is a collection of different types, that are used for interfacing generic
control commands. The types are mostly generic to certain domains (e.g.
underwater, UGV a.s.o).</p>

<h3 id="odometry">Odometry</h3>

<p>The base/odometry.h header contains interface classes, which can be implemented
by odometry models. There is a distinction between 2d and 3d, as well as
Gaussian and sampling models.</p>

<h3 id="waypoint">Waypoint</h3>

<p>Contains a class which represents a waypoint, which consists of a point, a
heading as well associated uncertainties. </p>

<h3 id="samples">Samples</h3>

<p>As mentioned above, samples are types time tagged types, which all contain a
time field of type <code>base::Time</code>. Samples are usually used to represent sensor or
state information. The following files are in the samples directory of the base
package. </p>

<ul>
<li>frame - is the basic class to represent image frames.</li>
<li>imu - contains imu sensor readings, which is a combination of accelerometer,
magnetometer and gyros</li>
<li>laser_scan - sample from a laser range finder (line)</li>
<li>pointcloud - basic datatype for representing point cloud information</li>
<li>rigid_body_state - is used to represent position and velocity information
between to reference frames including uncertainty.</li>
<li>sonar_scan - sample from a sonar range image (underwater)</li>
</ul>

<h3 id="actuators">Actuators</h3>

<p>TODO</p>


</div>
</div>
</div>
