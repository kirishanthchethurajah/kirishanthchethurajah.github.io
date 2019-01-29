---
layout: page
title: Introduction
section: Device Drivers
---
<div class="content2">
<div class="content2-pagetitle">Introduction</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>This section will discuss some of the aspects of writing device drivers in Rock.</p>

<p>Unfortunately, the guidelines exposed here are seldom followed in current Rock
packages themselves. The reason is that these guidelines grew from experience,
and from some requirements in some domains (namely, underwater).</p>

<p>Anyway, what this section will talk about is:</p>

<ul>
 <li>general driver structure, naming, separation between driver library
and driver oroGen component.</li>
 <li>usage of the
<a href="../../package_directory/packages/drivers/drivers_iodrivers_base/index.html">iodrivers_base</a>
package</li>
 <li>integration in oroGen components</li>
</ul>

<h2 id="structure">Structure</h2>
<p>Ideally, a device driver should be split into two parts:</p>

<ul>
 <li>a <strong>driver</strong> part that talks to the device using the device&rsquo;s protocol. This
driver has two functions. First, to provide access to the device&rsquo;s raw
functions (i.e. &ldquo;talk the device language&rdquo;). Second, translate the &ldquo;device
language&rdquo; into more high-level representations (as for instance translating
encoder ticks into angles in radians). This translation is not always
possible, but should be done whenever applicable.</li>
 <li>an <strong>oroGen component</strong> that integrates the driver into a task context, allowing
to be used in the rest of the toolchain. Note that Rock happily integrates
drivers that have no oroGen components, it is just recommended to provide
one.</li>
</ul>

<h2 id="naming-conventions">Naming conventions</h2>

<p>From a package point of view, there should be at least two packages: one for the
protocol/driver part and one for the oroGen part.</p>

<p>The naming convention for the library package is
<strong>sensor_type</strong>_<strong>manufacturer_and_or_sensor_name</strong>. For instance, cameras of the
&ldquo;prosilica&rdquo; brand share all the same protocols. The corresponding package is
therefore called camera_prosilica. All driver packages should be in drivers/
(i.e. drivers/camera_prosilica).</p>

<p>There is no standardization of the sensor types. <strong>Check existing packages</strong>
first, and ask around (i.e. on the mailing list) if your category does not
exist.</p>

<p>In the library package, the following guidelines should be followed:</p>

<ul>
 <li>namespace should be the same than package name</li>
 <li>if the package contains only one driver is to be defined, the driver class
should be called Driver. Otherwise, SpecificNameDriver (if applicable,
SpecificName should match between driver and protocol)</li>
</ul>

<p>Once a package name is selected for the library, the oroGen package should be
located in drivers/orogen/ and have the same base name than the library package.</p>

<p>Let&rsquo;s now dive into <a href="writing_driver.html">writing a driver class</a></p>



</div>
</div>
</div>
