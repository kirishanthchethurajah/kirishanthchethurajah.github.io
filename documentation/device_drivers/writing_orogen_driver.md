---
layout: page
title: oroGen Integration
section: Device Drivers
---
<div class="content2">
<div class="content2-pagetitle">oroGen Integration</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>Once you <a href="writing_driver.html">have written a driver based on
iodrivers_base::Driver</a>, you can think about integrating it
in an oroGen component.</p>

<p>Rock contains a generic integration of such a component: the iodrivers_base
oroGen package. This page is going to describe the usage of this class first,
and then describe the component&rsquo;s interface design.</p>

<h2 id="guidelines">Guidelines</h2>

<ul>
<li>the oroGen project should have the same name than the corresponding library
package.</li>
<li>if the device is periodic, the component should use <a href="../data_processing/timestamping.html">the
TimestampEstimator</a> to correct the
timestamps</li>
<li>use as much <a href="../base_types.html">base data types</a> as possible</li>
<li>common type names have also common port names. In general, it is good to
check devices of the same type and follow the same naming conventions.</li>
<li>
<p>ports that output types in the /base/samples namespace must end with
_samples, as e.g.</p>

<pre><code>position_samples
orientation_samples
</code></pre>
</li>
</ul>

<h2 id="usage">Usage</h2>

<p>To create a new driver component, first create the corresponding package using
the <a href="../tutorials/110_basics_create_component.html">rock-create-orogen tool</a>.
This component should depend on the <tt>drivers/orogen/iodrivers_base</tt>
package.</p>

<p>At the top of the oroGen file, add:</p>

<pre><code class="language-ruby">using_task_library "iodrivers_base"
</code></pre>

<p>Then, make your task a subclass of the iodrivers_base::Task task context:</p>

<pre><code class="language-ruby">task_context "Task" do
subclasses "iodrivers_base::Task"
end
</code></pre>

<p>There is two things left to do:</p>

<ul>
<li><strong>configureHook</strong>: create the device driver, open the device and call the
setDriver() method. The device&rsquo;s &ldquo;name&rdquo; (i.e. device file, IP for network-based access, &hellip;) is
provided in the io_port property. Note that it is legal for this property to
be empty (see the next section for explanations).</li>
</ul>

<pre><code class="language-cpp">setDriver(driver)
</code></pre>

<ul>
<li>
<p>process the incoming data in a &ldquo;void processIO()&rdquo; method that is going to be
called by the base class from updateHook().</p>
</li>
<li>
<p><strong>cleanupHook</strong>: the device&rsquo;s close() method is going to be called
automatically by the base class. The pointer is <em>not</em> going to be deleted
though, so you should take care of it if you want to recreate the object in
cleanup/configure cycles.</p>
</li>
</ul>

<p>For instance, one could have a setup looking like:</p>

<pre><code class="language-cpp">bool Task::configureHook()
{
Driver* driver = new Driver();
if (!_io_port.get().empty())
driver-&gt;openURI(_io_port.get());
setDriver(driver);

// This is MANDATORY and MUST be called after the setDriver but before you do
// anything with the driver
TaskBase::configureHook();

// If some device configuration was needed, it must be done after the
// setDriver and call to configureHook on TaskBase (i.e., here)
}
void Task::processIO()
{
mDriver-&gt;processSamples();
_samples.write(mDriver-&gt;getOrientationSample());
}
</code></pre>

<p>In addition, you might want to start data acquisition in the startHook and stop
it in the stopHook. Whether the acquisition start/stop should be in
startHook/stopHook or configureHook/cleanupHook is governed by the following
factor:</p>

<ul>
<li>if starting/stopping acquisition is done a lot quicker than the whole device
configuration, then do it in startHook/stopHook as you will not waste
ressources doing acquisition while the data is not needed</li>
<li>if it is slow (some people would even say: not deterministically fast, but
YMMV), do it in the configureHook/cleanupHook to ensure the responsiveness of
the system when start/stop cycles are needed but reconfiguration is not needed.</li>
</ul>

<p>By default, the standard runtime management of oroGen tasks entails that you
will have a full stop/cleanup/configure/start cycle if reconfiguration is
needed. You should therefore not care about dynamic reconfiguration in first
implementations.</p>

<h2 id="details-about-the-iodriversbasetask-interface">Details about the iodrivers_base::Task interface</h2>
<p>This interface provides two means of communication between the device and the
driver.</p>

<ul>
<li>direct I/O access. This is done by setting io_port to a non-empty string.
Acceptable values for io_port if the driver uses openURI (which it should do)
are listed in the property&rsquo;s documentation.</li>
<li>port-based access. In this mode, the data is flowing through the io_raw_in
and io_raw_out ports. The transfer of data between the ports and the Driver
object is made by the iodrivers_base::Task class.</li>
</ul>

<p>Other properties control the behaviour of the system in both modes (read
timeout) and write statistics about the I/O. Some properties are specific to one
mode, in which case this is documented in the property documentation directly.</p>



</div>
</div>
</div>
