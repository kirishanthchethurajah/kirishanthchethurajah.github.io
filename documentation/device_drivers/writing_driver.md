---
layout: page
title: Writing a device driver
section: Device Drivers
---
<div class="content2">
<div class="content2-pagetitle">Writing a device driver</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>This page will detail guidelines, and means of implementation, for device
drivers. While doing so, it dives into the usage of <a href="../../api/drivers/iodrivers_base">the
iodrivers_base::Driver</a>
class, which is the generic driver implementation in Rock.</p>

<p>The following examples are examples from the implementation of <a href="../../pkg/drivers/dvl_teledyne/index.html">the DVL Teledyne driver</a>.</p>

<h2 id="guidelines">Guidelines</h2>

<ul>
 <li>drivers should access the I/O in a timeout-based manner. In no cases should
the driver block forever on input or output.</li>
 <li>error notification should in general be exception-based</li>
 <li>reported measurements should be timestamped, either through a device-specific
mean or by using base::Time::now(). As stated
<a href="../data_processing/timestamp_estimator_usage.html">here</a>, the TimestampEstimator
class should not be used at the library level.</li>
 <li>multi-threading is in general not useful (and harmful as it adds a lot of
complexity). See the type 2 device/driver interaction below.</li>
</ul>

<h2 id="opening--closing-a-device">Opening / Closing a device</h2>

<p>A device is opened using its open method and closed using the close method. It
is in general a good policy to deinitialize the device itself (i.e. put it for
instance in a sleep state in close()).</p>

<ul>
 <li>close() should be without arguments and can call
iodrivers_base::Driver::close() to close the file descriptor.</li>
 <li>open can have an arbitrary number of arguments of any sort. In general, it is
good policy to use iodrivers_base::Driver::openURI(std::string) to get the
file descriptor, but that is not a strong requirement. Additionally, it is
expected that the device is in a good initial state. Ideally, it would be
left to a known state after open (i.e. where the configuration after the call to
open should not depend on previous configurations).</li>
</ul>

<h2 id="packet-extraction">Packet Extraction</h2>
<p>The iodrivers_base::Driver class implements a generic handling of packet-based
I/O. It does packet reassembly and packet extraction.</p>

<p>In order to keep hard realtime constraints within the drivers, the Driver class
does not do any memory allocation for its internal reassembly buffer. It is
therefore expected that the particular device drivers provide a maximum buffer
size. This size is supposed to be greater than the maximum packet size for the
device.</p>

<p>The first thing to do is therefore to initialize the Driver base class with that
value:</p>

<pre><code class="language-cpp">namespace dvl_rdi {
class Driver : public iodrivers_base::Driver {
public:
 Driver();
};
}
</code></pre>

<pre><code class="language-cpp">using namespace dvl_rdi;
Driver::Driver()
 : iodrivers_base::Driver(10000) {}
</code></pre>

<p>The second thing is to overload <a href="https://github.com/rock-drivers/drivers-iodrivers_base/blob/063e7a34602e7fb7dd2e14a761690a16df8f8229/src/Driver.hpp#L418">the extractPacket
method</a>.
This method parses a given data buffer and tries to find packets from the device
in it. See the API documentation linked above for a complete description.</p>

<h2 id="use-cases">Use Cases</h2>
<p>There are mainly two &ldquo;types&rdquo; of device/drivers interaction:</p>

<ol>
 <li>
   <p>the device is mostly request-based, i.e. the device sends a request and
has to wait for a particular reply. More generically, the driver user
(i.e. the code that is calling the driver) <em>knows</em> what message to
expect.</p>
 </li>
 <li>
   <p>the device returns different kind of packets in a not-so-well-defined
order (a GPS device with different reported NMEA sentences falls into
that category for instance).</p>
 </li>
</ol>

<h2 id="type-1">Type 1</h2>
<p>The API design guideline in this case is to simply have one call per message.
The call should either return the received data structure or, if memory
allocation is to be avoided and the data structure contains std::vector, pass it
as argument</p>

<pre><code class="language-cpp">double speed = getSpeedMeasurement();
void setSpeedMeasurement(double);
</code></pre>

<h2 id="type-2">Type 2</h2>
<p>For these, one has to maintain an internal data structure an notify the caller
as to what information has been received. In general, this is done through
a read(base::Time timeout) method.</p>

<p>The notification algorithm might be done in different ways:</p>

<ul>
 <li>implicitly by saving timestamped information in the driver itself. The caller
can then check whether the data has been updated or not.</li>
 <li>explicitely by making read() return an enum that informs what message has been received.</li>
</ul>

<h2 id="type-2-disguised-as-type-1">Type 2 disguised as Type 1</h2>
<p>For most usage of devices, one has a &ldquo;command phase&rdquo; and a &ldquo;sensing phase&rdquo;.
Both phases, taken separately, are often of a type 1 interaction (synchronous
request/reply). However, it is theoretically possible to receive sensing data
while sending commands, making the whole system, in principle, of type 2.</p>

<p>To keep things simple, in this case, one can simply ignore sensing messages
while doing commands, and having a simple interface to read command-related
messages (since they don&rsquo;t have to be treated asynchronously).</p>

<h2 id="summary">Summary</h2>

<p>To summarize, a minimal driver class should look like:</p>

<pre><code class="language-cpp">using namespace dvl_rdi;
Driver::Driver()
 : iodrivers_base::Driver(10000) {}
 // The count above is the maximum packet size

void Driver::open(std::string const&amp; uri)
{
   openURI(uri);
   // Do additional device setup
}

void Driver::read()
{
   uint8_t buffer[10000];
   int packet_size = readPacket(buffer, 10000);

   // parsePacket is a method that is specific to this driver.
   // It gets a packet, extracts the information from it and
   // updates the driver internal data structures
   parsePacket(buffer, packet_size);
}

// Reimplement close() only if you have specific things to do
// with your device before closing the I/O
void Driver::close()
{
   iodrivers_base::Driver::close();
}

int Driver::extractPacket(uint8_t const* buffer, size_t size) const
{
   // See the doxygen documentation of
   // iodrivers_base::Driver::extractPacket
   // to know what this method should do
}

// Finally, if you have to send commands to the device, always
// use writePacket (from iodrivers_base::Driver)
</code></pre>


</div>
</div>
</div>
