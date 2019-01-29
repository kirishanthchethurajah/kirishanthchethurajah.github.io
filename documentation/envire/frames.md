---
layout: page
title: Frame Tree
section: Envire
---
<div class="content2">
<div class="content2-pagetitle">Frame Tree</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="frame-tree">Frame Tree</h2>

<p>The frame tree stores information about cartesian reference frames that are
available in the environment. Every environment has a root frame, which can be
retrieved from an environment object using the <code>getRootNode()</code> method. </p>

<p>Additional frames can be added to the frame tree using one of the two methods</p>

<pre><code class="language-cpp">FrameNode *childFrame = new FrameNode(
  Eigen::Transform3d( Eigen::Translation3d( 0, 0, 1.0 ) ) );

env-&gt;addChild( env-&gt;getRootNode(), childFrame )
</code></pre>

<p>or alternatively</p>

<pre><code class="language-cpp">env-&gt;getRootNode()-&gt;addChild( childFrame );
</code></pre>

<p>The second method obviously only works if the parent framenode is already
attached to the environment.</p>

<h2 id="transformation">Transformation</h2>

<p>The transformation given in the constructor of the FrameNode denotes the
transformation from the given frame to the parent frame. For the above example,
a point with (0, 0, 0) in childFrame would be (0, 0, 1.0) in the parentFrame (in
this case the root Frame).</p>

<p>The transformation can be accessed using the <code>getTransform</code> and <code>setTransform</code>
methods of the FrameNode. </p>

<p>In order to obtain the transformation between two arbitrary frames (both of
which need to be connected to the same frame tree) the <code>relativeTransform</code>
method can be used. </p>

<pre><code class="language-cpp">Transform3d C_a2b = env-&gt;relativeTransform( frameA, frameB );
</code></pre>

<p>Will return the transform C_a2b, which given a vector v in frame A would provide
v&rsquo; = C_a2b * v, the same vector in frame B.</p>

<h2 id="uncertainty">Uncertainty</h2>

<p>The frame tree also supports uncertainty, but special methods have to be used
for that. Using the <code>setTransformWithUncertainty</code> method of the frame allows you
to provide a gaussian uncertainty of the transformation. The class for that is
<code>TransformWithUncertainty</code>, which additionaly to the transformation stores the
covariance of the position and rotation (as a scaled axis rotation) as a 6x6
matrix.</p>

<p>Chaining of transformations with uncertainty is also available, so you can see
how the uncertainty propages through the frames. A simple example would be the
following</p>

<pre><code class="language-cpp">// create a point with uncertainty.
// we provide sigma values of 0.01 m, which have to be squared,
// since the class is expecting a covariance
PointWithUncertainty p(
Eigen::Vector3d( 0.0, 0, 0 ),
Eigen::Vector3d( 0.01, 0.01, 0.01 )
    .array().square().matrix().asDiagonal() );

// create a 6x6 covariance matrix
Eigen::Matrix&lt;double,6,1&gt; lt1;
lt1 &lt;&lt;
  0.0, 0.0, 0.0, 0.0, 0.0, 0.0;

// create a transform with uncertainty from the given
// diagonal of sigma values
TransformWithUncertainty t1(
Eigen::Affine3d(
    Eigen::Translation3d(Eigen::Vector3d(r,0,0))),
lt1.array().square().matrix().asDiagonal() );

// and a second one
Eigen::Matrix&lt;double,6,1&gt; lt2;
lt2 &lt;&lt;
  0.0, 0.0, 0.1, 0.0, 0.0, 0.0;

TransformWithUncertainty t2(
Eigen::Affine3d(
    Eigen::Affine3d::Identity()),
lt2.array().square().matrix().asDiagonal );

// The point transformation is the same as without uncertainty,
// but the uncertainty information is also propagated
PointWithUncertainty pp = t2 * t1 * p;
</code></pre>

<p>The same works when storing the <code>TransformationWithUncertainty</code> in the frame
tree. Instead of <code>relativeTransform</code>, the <code>relativeTransformWithUncertainty</code>
can be used.</p>



</div>
</div>
</div>
