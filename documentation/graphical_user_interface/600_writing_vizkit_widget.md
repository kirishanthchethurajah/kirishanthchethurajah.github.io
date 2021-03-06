---
layout: page
title: Writing Vizkit Widgets
section: Graphical User Interface
---
<div class="content2">
<div class="content2-pagetitle">Writing Vizkit Widgets</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>This page deals with creating C++ widgets and registering them on the Ruby side to make
them available to Rock tooling (rock-replay, rock-display, &hellip;)</p>

<p>You might also want to follow the <a href="../advanced_tutorials/300_vizkit_widget.html">related tutorials</a>.</p>

<h2 id="writing-custom-ruby-widgets">Writing Custom ruby Widgets</h2>
<p>Vizkit can easily be extended with custom ruby widgets. To do so follow the steps below:</p>

<ul>
 <li>write a new ruby class which is implementing the custom behaviour</li>
 <li>register the widget so that Vizkit can find it </li>
</ul>

<p>For registering a new ruby widget call: Vizkit::UiLoader.register_ruby_widget
&ldquo;name_of_widget&rdquo;, factory_method</p>

<p>Parameters:</p>

<ul>
 <li>name_of_widget = any name which is used to access the widget </li>
 <li>factory_method = method which shall be used to create a new instance </li>
</ul>

<p>For registering a new widget for a specific data type call:
Vizkit::UiLoader.register_widget_for &ldquo;name_of_widgte&rdquo;, &ldquo;data_type&rdquo;, callback</p>

<p>Parameters:</p>

<ul>
 <li>name_of_widgte = name of the widget which shall be used for data display </li>
 <li>data_type = typlib data type for example &ldquo;/base/samples/frame/Frame&rdquo;</li>
 <li>callback = function which shall be called on the widget to display the sample</li>
</ul>

<p>After the widget is registered for a specific data type, a port which is of that
data type can be displayed by calling Vizkit.display port</p>

<h2 id="writing-custom-c-widgets">Writing Custom C++ Widgets</h2>
<p>Vizkit can easily be extended with custom c++ qt widgets. To do so follow the steps below:</p>

<ul>
 <li>write the custom qt c++ widget</li>
 <li>all setter and getter methods must be implemented as slots or signals with qt data types as parameters</li>
 <li>implement the QDesignerCustomWidgetInterface for the widget</li>
 <li>install the widget as shared library (install/lib/qt/designer/)</li>
 <li>write a ruby script to make the widget usable for Vizkit and require it after Vizkit is loaded</li>
</ul>

<p>Example: ruby script which integrates a c++ widget into vizkit</p>

<pre><code class="language-ruby">require 'eigen'

#the widget is automatically extended with the
#following methods if it is loaded via
#Vizkit.default_loader.widget_name

name = "ArtificialHorizon"
Vizkit::UiLoader.extend_cplusplus_widget_class name do
 #function which is called when new data are available
 def update(sample,port_name)
     # pose and rigid body state
     if sample.respond_to?(:orientation)
       sample = sample.orientation
     end

     if !sample.kind_of?(Eigen::Quaternion)
         # The base typelib plugin is not loaded,
         # do the convertions by ourselves
         sample = Eigen::Quaternion.new(sample.re,
                                        *sample.im.to_a)
     end

     angles = sample.to_euler(2,1,0)
     #call c++ slots to set the new orientation
     setPitchAngle(angles.y)
     setRollAngle(angles.z)
 end
end

#register widget to be automatically picked up when
#Vizkit.display is called
Vizkit::UiLoader.register_widget_for(name,
                 '/wrappers/Orientation',:update)
Vizkit::UiLoader.register_widget_for(name,
                 '/wrappers/RigidBodyState',:update)
Vizkit::UiLoader.register_widget_for(name,
                 '/wrappers/samples/RigidBodyState',:update)
Vizkit::UiLoader.register_widget_for(name,
                 '/wrappers/Pose',:update)
</code></pre>

<p><strong><em>NOTE</em></strong>:
For convenience, you can use the script <em>rock-create-vizkit-widget</em> which
basically does the steps listed above. Moreover, it generates all the necessary
code and build files for you and registers your widget with Qt Designer.
See <a href="../advanced_tutorials/300_vizkit_widget.html">here</a> for a detailed example.</p>


</div>
</div>
</div>
