---
layout: page
title: Available Widgets
section: Graphical User Interface
---
<div class="content2">
<div class="content2-pagetitle">Available Widgets</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>All standard qt and qwt widgets can be used from ruby. Furthermore rock is
shipped with a collection of widgets for some basic data analysis.</p>

<p><strong>ImageView:</strong>
Widget for displaying camera images (base/frame/frame, base/frame/FramePair)</p>

<pre><code class="language-ruby">  require 'vizkit'
 orocos.run 'my_camera' do
   camera = Orocos.name_service.get 'camera'
   camera.start
   Vizkit.display camera.frame
   Vizkit.exec
 end
</code></pre>

<p class="align-center"><img src="500_image_viewer.png" alt="ImageView" /></p>

<p><strong>PlotWidget:</strong>
Widget for plotting sensor data</p>

<pre><code class="language-ruby">  require 'vizkit'
 orocos.run 'my_sensor' do
   widget = Vizkit.default_loader.Plot2d
   sensor = Orocos.name_service.get 'sensor'
   sensor.configure
   sensor.start

   sensor.data.connect_to do |sample,_|
     widget.update sample.field2.to_f,"sample1"
     widget.update sample.field2.to_f,"sample2"
   end

   widget.show
   Vizkit.exec
 end
</code></pre>

<p class="align-center"><img src="500_plot2d.png" alt="Plot2d" /></p>

<p><strong>ArtificialHorizon:</strong>
Widget for visualizing a rigid body state</p>


</div>
</div>
</div>
