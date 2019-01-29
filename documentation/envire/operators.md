---
layout: page
title: Operators
section: Envire
---
<div class="content2">
<div class="content2-pagetitle">Operators</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="operators">Operators</h2>

<p>Operators provide the logic to transform map information, taking frame relations
into account. Every operator has input and output layers. The basic usage of an
operator would be the following:</p>

<pre><code class="language-cpp">// create a new Merge operator that combines the given maps and
// fills an output map with the merged information
MergeOperator *mo = new MergeOperator();

// assign input and output. operator and maps will automatically
// be attached if they are not yet.
env-&gt;addInput( mo, pc1 );
env-&gt;addInput( mo, pc2 );
env-&gt;addOutput( mo, pcOut );

// perform the update operation on the full maps
// (partial updates not yet supported)
mo-&gt;updateAll();
</code></pre>



</div>
</div>
</div>
