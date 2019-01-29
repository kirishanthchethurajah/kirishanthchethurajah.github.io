---
layout: page
title: Triggering
section: Developing Components
secondsection: FD-driven tasks
---
<div class="content2">
<div class="content2-pagetitle">FD-driven tasks</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>The IO triggering mechanism, if enabled, will make sure that updateHook() is
called whenever new data is made available on a file descriptor. It allows to
very easily implement drivers, that are waiting for new data on the driver
communication line(s).</p>

<h2 id="declaration">Declaration</h2>
<p>The IO-driven mechanism is a deployment choice, and therefore it is not required
to declare a task context as IO-driven. Nonetheless, since some actions are
required from the task context&rsquo;s implementation (namely listing the IOs the task
is listening to), declaring them as IO-driven can help deployment and usage of
the said tasks (since people will know that they may be used as IO-driven).</p>

<p>To declare a task context as IO-driven, one adds the following statement to the
task context definition block:</p>

<pre><code class="language-ruby">fd_driven
</code></pre>

<p>In this case, the io-driven triggering mechanism is declared as optional. If it
is required, then do</p>

<pre><code class="language-ruby">required_activity :fd_driven
</code></pre>

<p>Note that deploying a task with <a href="periodic.html">a periodic activity</a> will
disable the fd-driven behaviour. However, the fd-driven activity can be overlaid
on top of the <a href="ports.html">port-driven triggering</a></p>

<h2 id="mandatory-setup-in-the-c-task">Mandatory setup in the C++ task</h2>

<p>On the C++ side, the triggering mechanism relies on a specific activity type,
i.e. it puts the component in its own thread.</p>

<p>To access more detailed information on the trigger reason, and to set up the
trigger mechanism, one must access the underlying activity. Two parts are
needed, one in startHook() to tell the activity which file descriptors to watch
for, and one in stopHook() to remove all the watches (<strong>that last part is
mandatory</strong>)</p>

<p>First of all, include the header in the task&rsquo;s cpp file:</p>

<pre><code class="language-cpp">#include &lt;rtt/extras/FileDescriptorActivity.hpp&gt;
</code></pre>

<p>Second, set up the watches in startHook</p>

<pre><code class="language-cpp">bool MyTask::startHook()
{
    // Here, "fd" is the file descriptor of the underlying device
    // it is usually created in configureHook()
    RTT::extras::FileDescriptorActivity* activity =
        getActivity&lt;RTT::extras::FileDescriptorActivity&gt;();
    if (activity)
        activity-&gt;watch(fd);
    return true;
}
</code></pre>

<p>This only works <em>if the activity is required</em>. Indeed, with another activity,
the component will crash as getFileDescriptorActivity() returns NULL;</p>

<p>If the use of an io-driven activity is optional, simply test for activity:</p>

<pre><code class="language-cpp">bool MyTask::startHook()
{
    // Here, "fd" is the file descriptor of the underlying device
    // it is usually created in configureHook()
    RTT::extras::FileDescriptorActivity* activity =
        getActivity&lt;RTT::extras::FileDescriptorActivity&gt;();
    if (activity)
        activity-&gt;watch(fd);
    return true;
}
</code></pre>

<p>It is possible to list multiple file descriptors by having multiple calls to
watch(). </p>

<p>It is possible to set a timeout in milliseconds with</p>

<pre><code class="language-cpp">    activity-&gt;setTimeout(100);
</code></pre>

<p>Finally, you <strong>must</strong> clear all watches in stopHook():</p>

<pre><code class="language-cpp">void MyTask::stopHook()
{
    RTT::extras::FileDescriptorActivity* activity =
        getActivity&lt;RTT::extras::FileDescriptorActivity&gt;();
    if (activity)
        activity-&gt;clearAllWatches();
}
</code></pre>

<h2 id="runtime-use-in-updatehook">Runtime use in updateHook()</h2>

<p>The FileDescriptorActivity class offers a few ways to get more information
related to the trigger reason (data availability, timeout, error on a file
descriptor). These different conditions can be tested with:</p>

<pre><code class="language-cpp">RTT::extras::FileDescriptorActivity* fd_activity =
    getActivity&lt;RTT::extras::FileDescriptorActivity&gt;();
if (fd_activity)
{
  if (fd_activity-&gt;hasError())
  {
  }
  else if (fd_activity-&gt;hasTimeout())
  {
  }
  else
  {
    // If there is more than one FD, discriminate. Otherwise,
    // we don't need to use isUpdated
    if (fd_activity-&gt;isUpdated(device_fd))
    {
    }
    else if (fd_activity-&gt;isUpdated(another_fd))
    {
    }
  }
}
</code></pre>



</div>
</div>
</div>
