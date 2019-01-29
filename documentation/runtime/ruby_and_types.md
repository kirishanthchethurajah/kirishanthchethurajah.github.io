---
layout: page
title: oroGen types and Ruby scripts
section: Running Components
---
<div class="content2">
<div class="content2-pagetitle">oroGen types and Ruby scripts</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>When you develop oroGen components, you declare C++ types. However, since the
system deployment in Rock is done in Ruby, we have to understand how the oroGen
C++ types are accessed on the Ruby side.</p>

<p>This page will explain:</p>

<ul>
<li>the mapping between C++ and Ruby in Rock</li>
<li>how to customize that mapping to get more functionality out of your Ruby
scripts</li>
</ul>

<h2 id="general-api">General API</h2>
<p>To create a new value, one has to first get the type definition. Type
definitions available on the oroGen components are exported in the Types
namespace:</p>

<pre><code class="language-ruby">time = Types::Base::Time.new
</code></pre>

<p>Note that this mechanism takes into account custom Ruby-to-Typelib conversions
(see below). For instance, by default, /base/Time gets converted to and from
Ruby Time. Therefore, the class Types::Base::Time is the same as Time.</p>

<p>In most cases, accessing the types directly is not necessary:</p>

<ul>
<li>when modifying a complex type in a property, <a href="properties.html">one should use the block form</a>
modify it and write it back</li>
<li>when writing to an input port, the InputPort#new_sample method creates a new
value object suitable for this particular port</li>
</ul>

<p>One big difference between C++ and Ruby is that, to copy a value on another,
one <strong>CANNOT</strong> do</p>

<pre><code class="language-ruby">value1 = value2 # THIS DOES NOT WORK
</code></pre>

<p>due to how variables behave in Ruby. Copying is done with</p>

<pre><code class="language-ruby">Typelib.copy(target, source)
</code></pre>

<h2 id="mapping-between-c-and-ruby">Mapping between C++ and Ruby</h2>
<p>In general, when an orocos.rb method returns a value of a certain C++ type, that
value will behave &ldquo;as it should&rdquo;, i.e.:</p>

<h3 id="structures">Structures</h3>

<p>They are represented as structures. For instance values of the type</p>

<pre><code class="language-cpp">namespace base {
class Time
{
    int seconds;
    int microseconds
};
}
</code></pre>

<p>can be accessed naturally with</p>

<pre><code class="language-ruby">puts time.seconds + time.microseconds
time.seconds *= 2
# and so on, and so forth
</code></pre>

<p>A structure can be initialized from a hash:</p>

<pre><code class="language-ruby">time_t.new(:seconds =&gt; 10, :microseconds =&gt; 20)
</code></pre>

<h3 id="arrays">Arrays</h3>

<p>They behave like Ruby arrays:</p>

<pre><code class="language-ruby">array_of_time[4].seconds = 10
array_of_time.find { |v| v.seconds &gt; 10 }
</code></pre>

<p>Arrays can be initialized from Ruby arrays:</p>

<pre><code class="language-ruby">array_of_time_t.new([time1, time2])
</code></pre>

<h3 id="stdvector">std::vector</h3>

<p>Values based on std::vector are mapped to an enumerable. You cannot access the
elements randomly, though</p>

<pre><code class="language-ruby">vector_of_time.each do |value|
...
end
</code></pre>

<p>if you want to access the elements by their index, convert the vector to an
array first</p>

<pre><code class="language-ruby">array = vector_of_time.to_a
</code></pre>

<p>Finally, the clear and insert methods allow you to modify the vector. Moreover,
vectors can be initialized from arrays of compatible types.</p>

<h3 id="enums">Enums</h3>

<p>They are accessed by the symbol name. The returned value is a symbol, and the
enum can be assigned from an integer value, a string or a symbol. For instance</p>

<pre><code class="language-cpp">namespace base {
namespace actuators {
    enum DRIVE_MODE {
        DM_UNKNOWN = -1,
        DM_PWM = 0,
        DM_SPEED = 1,
        DM_POSITION = 2
    }

    struct Command {
        DRIVE_MODE mode;
    };
}
}
</code></pre>

<p>can be accessed with</p>

<pre><code class="language-ruby">command.mode = 'DM_PWM'
command.mode =&gt; :DM_PWM # beware, this is a Ruby symbol !!!
</code></pre>

<h3 id="opaque-types-advanced-subject">Opaque Types (ADVANCED SUBJECT)</h3>

<p>The opaque types are manipulated, on the Ruby side, through their intermediate
type. For instance, if a property of type
<a href="../../package_directory/orogen_types/_base_Vector3d.html">base::Vector3d</a> is created, it will be
accessed as a structure <a href="../../package_directory/orogen_types/_wrappers_Matrix__double_3_1_.html">of the corresponding
type</a></p>

<h2 id="customization-on-the-ruby-side">Customization on the Ruby side</h2>
<p>So, we now know how to manipulate the C++ types from within Ruby. However, the
types are pretty &lsquo;plain&rsquo;. I.e., they offer no nice ways to be manipulated.</p>

<p>There are two ways to customize the C++ to Ruby mapping:</p>

<ul>
<li>either by adding methods to the values. For instance, one could define the #+
method on &lsquo;/base/Time&rsquo;, which would add two times together</li>
<li>or by specifying convertions between some Ruby class and the
oroGen-registered types. For instance, converting between /base/Time and the
builting Time class in Ruby</li>
</ul>

<p>To add methods to an oroGen-registered type, one does</p>

<pre><code class="language-ruby">Typelib.specialize '/base/Time' do
def +(other_time)
   # add the two times together and return the result
end
end
</code></pre>

<p>To allow convertion between a Ruby class and an oroGen-registered type, one does</p>

<pre><code class="language-ruby"># If we get a /base/Time, convert it to Ruby's Time class
Typelib.convert_to_ruby '/base/Time' do |value|
Time.at(value.seconds, value.microseconds)
end
# Tell Typelib that Time instances can be converted into /base/Time values
Typelib.convert_from_ruby Time, '/base/Time' do |value, typelib_type|
result = typelib_type.new
result.seconds      = value.tv_sec
result.microseconds = value.tv_usec
result
end
</code></pre>

<p class="note">See <a href="https://github.com/rock-core/base-types/blob/master/bindings/ruby/lib/base/typelib_plugin.rb">the typelib_plugin.rb</a>
file from the base/types package as an example.</p>

<p>You can safely add a convert_to_ruby specification late in the
development process, without breaking the scripts that were accessing the
structures. Indeed, the converted value is wrapped in a way that makes possible
accessing the structure fields (seconds and microseconds) or accessing it as a
Time object.</p>

<p class="note"><strong>Note</strong> when developing with Rock, it is recommended to put the typelib
C++-to-Ruby customization code in a scripts/typelib.rb in the oroGen components.
This file will get installed automatically along with the oroGen code, in a
place where it gets found and loaded automatically by typelib. Thus, the
convertion code is made available to all Ruby scripts that use orocos.rb/orogen</p>

<h2 id="direct-access-to-the-type-models">Direct Access to the Type Models</h2>

<p>The &lsquo;Types::&rsquo; export interface provides an easy-to-use access to the types.
However, it one sometimes needs access to the &ldquo;raw&rdquo; typelib models.</p>

<p>In orocos.rb, all types loaded for the benefit of accessing oroGen components
are saved in the singleton Typelib::Registry instance saved in Orocos.registry.
Therefore, to get the typelib model for type &lsquo;/base/XXX&rsquo;, one needs to do</p>

<pre><code class="language-ruby">Orocos.registry.get("/base/XXX")
</code></pre>


</div>
</div>
</div>
