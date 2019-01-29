---
layout: page
title: Data Visualization
section: Advanced
---

<div class="content2">
<div class="content2-pagetitle">Data Visualization</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<h2 id="abstract">Abstract</h2>
<p>In this tutorial, you will learn how to display live and logged data with the help of Vizkit and how to
write a custom GUI to display the data of your orocos task.</p>

<p>To understand this tutorial, you must have completed the basic tutorials and the data
logging tutorial. Basic knowledge about Qt, Qt Designer and qtruby is also
necessary.</p>

<p>This tutorial directly builds on the previous tutorials. Additionally, you have
to have the rock packages gui/vizkit and gui/rock_widget_collection installed.</p>

<p>In case you are starting here, the required packages are mentioned on the <a href="index.html">introduction page</a>.</p>

<h2 id="displaying-data-with-vizkit">Displaying Data with Vizkit</h2>
<p>Vizkit is a ruby framework built around qtruby to support the developer with functionality to display his
custom data in a convenient way. It is shipped with some Qt Widgets to display some basic types.
But its real strength lies in the ability to be customized.</p>

<p><strong>Displaying Camera Samples</strong> For some base types like the camera frame, Vizkit has a special display widget.
To create an instance of a widget specialised to display camera frames, you can use the Vizkit ruby
method <em>display</em> and an Orocos port which is producing camera frames as an argument. In the case below, the camera is
substituted by a log file called &ldquo;bottom_camera.0.log&rdquo;, but you can use the same syntax for Orocos tasks as well.</p>

<pre><code class="language-ruby">require 'orocos'
require 'vizkit'

include Orocos
Orocos.initialize

log = Orocos::Log::Replay.open("bottom_camera.0.log")

Vizkit.display log.bottom_camera.frame
Vizkit.control log
Vizkit.exec
</code></pre>

<p>For testing purposes, a small camera log file can be found <a href="https://github.com/rock-tutorials/tutorials-advanced_data_visualization/raw/master/logfiles/bottom_camera.0.log">here</a>. It shows an underwater test pipeline used during a student competition in Italy, <a href="http://www.sauc-europe.org/">Sauc-E</a>.</p>

<p class="align-center"><img src="210_image_viewer.png" alt="ImageView Widget" /></p>

<h2 id="designing-a-custom-gui">Designing a custom GUI</h2>
<p>Vizkit is able to load ui files created with the Qt Designer. In this case, a small GUI is loaded whose ui file
can be found <a href="https://github.com/rock-tutorials/tutorials-advanced_data_visualization/raw/master/resources/test_gui.ui">here (right click - save as)</a>. If you want to create your own ui file, use the <em>Qt
Designer</em>, e.g. by calling <em>designer</em> on the command line.</p>

<p>This ui basically creates a window with two dedicated labels for writing strings
(named <em>Time</em> and <em>Message</em>) and a push button (named <em>Button</em>) to trigger
actions. Further on, this file is referred to as <em>test_gui.ui</em>.</p>

<p>To load the ui file with Vizkit, use the Vizkit ruby method <em>load</em>. After the ui file is loaded,
you can access all embedded objects by their object name and their qt signal/slots. </p>

<pre><code class="language-ruby">require 'vizkit'
#load ui file
gui = Vizkit.load 'test_gui.ui'

#do something if someone is clicking on the
#button named Button
gui.Button.connect(SIGNAL('clicked()')) do
    puts 'someone presses the button'
    gui.Time.setText(Time::now.to_s)
end

#display something in the label named Time
gui.Time.setText(Time::now.to_s)
gui.Message.setText("Just a message!")

gui.show
Vizkit.exec
</code></pre>

<p class="align-center"><img src="210_message.png" alt="Message Widget" /></p>

<p>As you can see, this small script displays the current time and a
message. Each time the button is pressed, the message is displayed on the command
line and the time label is updated.</p>

<h2 id="connecting-a-custom-gui-to-an-orocos-task">Connecting a custom GUI to an orocos task</h2>

<p>Just running the ui is nice for testing purposes, but the more interesting part
is connecting it to a real orocos task.</p>

<p>Use the following script as a basic example for the interaction between a
vizkit-ui and an orocos task.</p>

<pre><code class="language-ruby">require 'vizkit'

include Orocos
Orocos.initialize

gui = Vizkit.load 'test_gui.ui'

Orocos.run 'faster_message_producer' do
    producer = Orocos::Async.name_service.get 'producer'
    prod_msg = producer.port "messages"

    producer.configure

    #change a property when the button is pressed
    gui.Button.connect(SIGNAL('clicked()')) do
        # First: stop and cleanup the producer task
        producer.stop
        producer.cleanup

        # change the configuration (uncomment if your code supports it)
        #producer.config do |p|
        #    p.uppercase = !p.uppercase
        #end

        # start the task again
        producer.configure
        producer.start
    end

    # start the task
    producer.start

    # connect the port of message producer to our ui
    prod_msg.on_data do |data|
        gui.Time.setText data.time.to_s
        gui.Message.setText data.content
    end

    # start vizkit
    gui.show
    Vizkit.exec
end
</code></pre>

<p>We do several things here which require some explanation:</p>

<ul>
  <li>
    <p>As the action of the button we want to switch the configuration of the
message producer. To do this we have to stop our current task and clean up
the configuration. Then we change the config, configure the task again and
start it. This scheme is necessary since it is currently the only safe way
to change the configuration of a task. You will notice a slight pause in the
time display when pressing the button, which is caused by this sequence.</p>
  </li>
  <li>
    <p>We connect our two labels with the output port of the message producer. </p>
  </li>
</ul>

<h2 id="connecting-a-custom-gui-to-log-files">Connecting a custom GUI to log files</h2>
<p>If you want to connect your GUI to a log file, you can use the same method
as for an orocos task. The following script connects test_gui.ui to a
component, which uses the log from the <a href="200_replay_1.html">previous
tutorial</a>. Since we only display data, the button is
not connected.</p>

<pre><code class="language-ruby">require 'vizkit'

log = Orocos::Log::Replay.open('producer.0.log')
gui = Vizkit.load 'test_gui.ui'

# connect the port of message producer to our ui
log.producer.messages.on_data do |data|
    gui.Time.setText data.time.to_s
    gui.Message.setText data.content
    data # never forget this!!!
end

gui.show
Vizkit.control log
Vizkit.exec
</code></pre>

<h2 id="summary">Summary</h2>
<p>In this tutorial, you learned how to write a simple GUI and how to
connect its object to log files and orocos tasks. </p>

<p>Progress to the <a href="220_3d_data_visualization.html">next tutorial</a>.</p>


</div>
</div>
</div>
