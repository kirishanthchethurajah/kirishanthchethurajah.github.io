---
layout: page
title: Multi Widget Display

section: Advanced
---

<div class="content2">
<div class="content2-pagetitle">Multi Widget Display</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="abstract">Abstract</h2>

<p>In this tutorial you will learn how to display multiple widgets in a configurable grid using the CompoundDisplay widget. </p>

<h2 id="compounddisplay">CompoundDisplay</h2>

<p>The CompoundDisplay enables you to display multiple Rock widgets in one container. This container is designed as a two-dimensional grid. Each element of the grid holds one widget and is identified by its &lsquo;position&rsquo;. This is a running index beginning at 0 going row-by-row from left to right. The grid dimensions (amount of rows and columns) are configurable from the script.</p>

<p class="align-center"><img src="620_compound_display_example_small.png" alt="ImageView Widget" /></p>
<p class="caption">Example of the CompoundDisplay in action. You see five display widgets on positions 0-4 and an empty slot on position 5.</p>

<p>One benefit of the CompoundDisplay is its ability to backup and restore your display widget configuration. That means you assign the widgets of your choice to specific grid coordinates and export this configuration to a YAML file. This feature is accessible through a slot or the optional toolbar (not shown in the picture). The configuration also includes information about the specific data sources, i.e. the task, port host of the data provider. Configurations can be either loaded upon startup (via script) or at runtime.</p>

<pre><code class="language-ruby">---
0: !ruby/object:CompoundDisplayConfig
 task: 10.250.8.27/camera1
 port: frame
 widget: ImageView
 connection_policy: {}
1: !ruby/object:CompoundDisplayConfig
 task: 10.250.8.27/camera2
 port: frame
 widget: ImageView
 connection_policy: {}
2: !ruby/object:CompoundDisplayConfig
 task: 10.250.8.27/xsens_imu
 port: orientation_samples
 widget: OrientationView
 connection_policy: {}
3: !ruby/object:CompoundDisplayConfig
 task: 10.250.8.27/laser_sick
 port: scan
 widget: Plot2d
 connection_policy: {}
4: !ruby/object:CompoundDisplayConfig
 task: 10.250.8.27/laser_hokuyo
 port: scans
 widget: Plot2d
 connection_policy: {}
5:
</code></pre>

<p class="caption">The corresponding YAML configuration file to the picture above.</p>

<p>Besides configuring the grid in your script or YAML file, you can drag and drop output ports directly from the TaskInspector to the grid elements. A context menu lets you choose a suitable display widget. After you picked one, the widget will be displayed and automatically connected to the sender. This also works with rock-replay.</p>

<p class="warning"><strong><em>IMPORTANT</em></strong>:
To use the Drag&amp;Drop feature, both the source and destination widget, i.e. TaskInspector and CompoundDisplay, have to be started in the same process. Simply start the TaskInspector in your script, as well, instead of using <em>rock-display</em>.</p>

<p>The following script shows a basic example of how to use CompoundDisplay with multiple remote hosts.</p>

<pre><code class="language-ruby">require 'vizkit'

Orocos.initialize

# Add as many (remote) hosts as necessary.
hosts = []
hosts &lt;&lt; "localhost"
# ...

# Add every host to the name service.
# We use an asynchronous connection for the GUI.
hosts.each do |host|
   Orocos::Async.name_service &lt;&lt; Orocos::Async::CORBA::NameService.new(host)
end

# Initialize and configure CompoundDisplay widget.
compound_display = Vizkit.default_loader.CompoundDisplay
compound_display.set_grid_dimensions(2,4) # 2 rows, 4 columns
#compound_display.show_menu(false)
compound_display.show

use_yaml = ARGV[0]

# Either configure CompoundDisplay by a predefined or previously exported
# YAML file or do it manually in the script. The connections are established
# automatically.
if use_yaml
   compound_display.configure_by_yaml("path_to_config.yml")
else
   # Manual configuration.
   compound_display.configure(
           0, "front_camera", "frame", "ImageView")
   compound_display.configure(
           2, "message_producer", "messages", "OrientationView")
end

# Initialize TaskInspector. You can drag output ports
# from there to the CompoundDisplay.
inspector = Vizkit.default_loader.TaskInspector
inspector.show

# Add the previously defined name service. Do this only once, since we
# already appended all remote hosts to the name service before.
inspector.add_name_service(Orocos::Async.name_service)

Vizkit.exec
</code></pre>


</div>
</div>
</div>
