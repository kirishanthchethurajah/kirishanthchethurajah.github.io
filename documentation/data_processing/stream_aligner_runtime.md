---
layout: page
title: ... at Runtime
section: Data Processing
---
<div class="content2">
<div class="content2-pagetitle">  ... at Runtime</div>
<div class="content2-container line-box">
<div class="content2-container-1col">


<p>At runtime, components that are using the stream aligner behave more or less
like normal components (when one considers that they can introduce latency in
the data processing chain, of course).</p>

<p>The integrator of such a component should, though, be careful to set the stream
periods properly. As noted <a href="stream_aligner.html">in the data processing
overview</a>:</p>

<p class="warning">Because processing based on the stream aligner is based on the fact that samples
are passed in-order, the stream aligner <strong>must</strong> drop samples that arrive &ldquo;in
the past&rdquo;, i.e. that arrive with an earlier timestamp that the last sample
&ldquo;played&rdquo; by the stream aligner. So, it is better to give a period that is
<strong>lower</strong> than the actual sensor period so that the aligner does not drop
samples unnecessarily.</p>

<p>The choice of the timeout is also important, but not so critical: the timeout is
a last resort mean to keep the data flowing. One should prefer using <a href="../system">advanced
means of monitoring</a>.</p>

<h2 id="monitoring">Monitoring</h2>
<p>The stream aligner allows to export a pretty complete statistics structure of
type <a href="/types//aggregator/StreamAlignerStatus.html">/aggregator/StreamAlignerStatus</a>, that informs the outside world about the
general aligner behaviour. The oroGen plugin exports that structures on an
output port called stream_aligner_status that exports this data structure. By
default, it is exported every second but this can be configured with the
stream_aligner_status_period property.</p>

<p>The data structure is very comprehensive. However, the main &ldquo;sanity&rdquo; variables
are the dropped count. See the type documentation (linked above) for more
details.</p>



</div>
</div>
</div>
