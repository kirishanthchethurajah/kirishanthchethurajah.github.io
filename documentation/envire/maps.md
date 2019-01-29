---
layout: page
title: Maps
section: Envire
---
<div class="content2">
<div class="content2-pagetitle">Maps</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="maps">Maps</h2>

<p>While frames relate cartesian frames to each other, maps are the objects in
which the actual information about the environment is stored. </p>

<p>All maps in envire are of the base class <code>Layer</code>. The layer class provides
information on the dirty status of a map, so if a map has been modified or not,
and can store generic metadata. See the API doc for a class hierarchy of the
Layer.</p>

<p>Layers itself do not contain any spatial information, so they can in principle
store both cartesian as well as non-cartesian (topological) maps. The derived
class <code>CartesianMap</code> does provide the ability to associate a layer with a frame.
This is done through either </p>

<pre><code class="language-cpp">env-&gt;setFrameNode( map, frameNode );
</code></pre>

<p>alternatively you can use the convenience method on the map directly, if it is
already attached.</p>

<pre><code class="language-cpp">env-&gt;attachItem( map );
map-&gt;setFrameNode( frameNode );
</code></pre>

<p>Each CartesianMap is associated with a frame, and the information it has is
given in that frame. Further down the inheritance chain, there is the <code>Map</code>
class, which is templated for the map dimension. Currently only <code>Map&lt;2&gt;</code> and
<code>Map&lt;3&gt;</code> are used. </p>

<h2 id="metadata">Metadata</h2>

<p>Each Layer can have associated metadata, which may or may not be used by the
processing algorithms (operators). </p>

<pre><code class="language-cpp">if( map-&gt;hasData( key ) )
{
DataType &amp;d = map-&gt;getData&lt;DataType&gt;( key );

... // do stuff on d
}
</code></pre>

<p>a call to <code>getData&lt;Type&gt;</code> will create the metadata if it is not yet created.
Metadata on maps is used to specify optional information that can be available
for specific maps, but which might not be available for all forms of that
particular map. This was chosen instead of inheritance to simplify handling of
different permutations of available data.</p>

<p>Some maps may provide additional features for metadata, like e.g. in a
<code>Pointcloud</code>. Here vertices are stored as a vector of <code>Vector3</code> objects. However,
these vertices may also have associated normals. </p>

<pre><code class="language-cpp">if( pc-&gt;hasData( Pointcloud::VERTEX_NORMALS ) )
{
std::vector&lt;Eigen::Vector3d&gt; normals =
pc-&gt;getVertexData&lt;Eigen::Vector3&gt;(
Pointcloud::VERTEX_NORMALS );

// both vectors should be of the same length
assert( pc-&gt;vertices.size() == normals.size() );

// perform a calculation on vertex normal pairs
for( size_t n=0; n&lt;pc-&gt;vertices.size(); n++ )
doCalc( pc-&gt;vertices[n], normals[n] );
}
</code></pre>

<h2 id="examples-of-maps">Examples of Maps</h2>

<p>There is a large number of maps available in envire, the best is to have a look
at the API docs. In principle, one can distinguish between dense and sparse
maps. E.g. a <code>Pointcloud</code> is a sparse map, while a <code>Grid</code> is a dense 2d map.</p>



</div>
</div>
</div>
