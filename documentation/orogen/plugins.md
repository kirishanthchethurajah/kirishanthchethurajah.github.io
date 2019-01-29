---
layout: page
title: Advanced - Writing oroGen plugins
section: Developing Components
---
<div class="content2">
<div class="content2-pagetitle">Advanced - Writing oroGen plugins</div>
<div class="content2-container line-box">
<div class="content2-container-1col">



<p>oroGen plugins offer ways to add new statements to the oroGen specification
file, allowing to integrate some libraries in the component development
workflow.</p>

<p>TODO: add links to the aggregator and transformer plugins</p>

<h2 id="general-principle">General principle</h2>
<p>The general plugin design is based on the extensible nature of the Ruby
language. How the plugin loading works is:</p>

<ul>
<li>the OROGEN_PLUGIN_PATH environment variable contains directories in which
plugin files (actually, ruby files) are.</li>
<li>these ruby files get loaded by oroGen at startup through the normal &ldquo;require&rdquo;
mechanism</li>
<li>the code in the Ruby file should extend the toplevel classes (Project,
Typekit, TaskContext, StaticDeployment) to add new functionality. Examples
can be found at the bottom of this page.</li>
</ul>

<p class="warning">This is the general principle. In practice, only the TaskContext class allows
seamless integration as it is the only one that has an API for code generation.</p>

<p>Example of a very simple plugin:</p>

<pre><code class="language-ruby">class BoolPlugin &lt;  OroGen::Spec::TaskModelExtension
  attr_accessor :varname

  # implement extension for task
  def pre_generation_hook(task)
      task.add_base_member("simple_plugin", varname, "bool")
      task.operations["getBool"].
          base_body("return #{varname};")
  end
end

class OroGen::Spec::TaskContext
  def add_boolean_attribute(varname)
      extension_name = "BoolPlugin"
      # find previous instance of the extension
      extension = find_extension(extension_name)
      if !extension
          # create new instance
          boolp = BoolPlugin.new(extension_name)
          boolp.varname = varname
          # define interface for task
          hidden_operation("getBool").
              returns("bool")
          register_extension(boolp)
      else
          raise OroGen::ConfigError, "Plugin '#{extension_name}' is already instantiated with base member '#{extension.varname}'. '#{varname}' will not be created."
      end
  end
end
</code></pre>

<p>If this plugin is present, the add_boolean_attribute method becomes available on
task context definitions. Then, the generated Base class for these tasks will
include a boolean attribute with the name given as parameter. Below, it would be
&ldquo;test&rdquo;.</p>

<pre><code class="language-ruby">task_context "Task" do
  add_boolean_attribute "test"
end
</code></pre>

<p>Edit .orogen/tasks/TaskBase.hpp to see the new attribute.</p>

<h2 id="adding-methods-to-task-contexts">Adding methods to task contexts</h2>

<p>The add_base_method and add_user_method methods allow to add virtual methods to
(respectively) the user-visible part of a task context and the Base class of the
task context.</p>

<p>The syntax (simply replace add_base_method by add_user_method to have the same
behaviour on the user-part of the task class)</p>

<pre><code class="language-ruby">add_base_method(return_type, name, signature)
</code></pre>

<p>This method call declares a pure virtual method (no body has been specified
yet). To give a body, one has to call #body on the value returned by
add_base_method:</p>

<pre><code class="language-ruby">add_base_method(return_type, name, signature).
  body(body_of_the_method)
</code></pre>

<p>Where the body is the method&rsquo;s implementation without the toplevel block markers</p>

<p>For instance, the getModelName method that oroGen adds to every task context is
defined with</p>

<pre><code class="language-ruby">add_base_method("std::string", "getModelName", "").
  body("return \"#{task_model.name}\";")
</code></pre>

<h2 id="adding-attributes-to-the-base-class">Adding attributes to the base class</h2>

<pre><code class="language-ruby">add_base_member("kind", "attribute_name", "attribute_type")
</code></pre>

<p>Additionally, related code can be added to the initializer list, constructor
and/or destructor:</p>

<pre><code class="language-ruby">add_base_member("kind", "attribute_name", "attribute_type").
  initializer("code_in_initializer_list").
  constructor("code_in_constructor").
  destructor("code_in_destructor")
</code></pre>

<p>+kind+ is a simple string that is only used for sorting the declarations. For
instance the properties use &ldquo;properties&rdquo; as &ldquo;kind&rdquo; so that all properties get
grouped together.</p>

<h2 id="adding-code-to-the-base-class-constructor-and-destructor">Adding code to the base class constructor and destructor</h2>
<p>It is simply done with:</p>

<pre><code class="language-ruby">add_base_construction(kind, name, body)
add_base_destruction(kind, name, body)
</code></pre>

<p>+kind+ and +name+ will have no meaning. They will only be used for sorting the
code snippets, so that the generated code always stays the same (avoiding
unnecessary recompilations).</p>

<h2 id="adding-code-the-hooks-in-the-base-class">Adding code the hooks in the base class</h2>

<p>It is done with</p>

<pre><code class="language-ruby">in_base_hook(hook_name, code)
</code></pre>

<p>For instance, oroGen clears all input ports in the base startHook() with</p>

<pre><code class="language-ruby">each_input_port do |port|
  in_base_hook('start', "_#{port.name}.clear();")
end
</code></pre>

<h2 id="adding-code-at-the-toplevel">Adding code at the toplevel</h2>

<p>Code can be added at the toplevel of the Base.hpp and Base.cpp files. Simply do</p>

<pre><code class="language-ruby">add_base_header_code(code, include_before)
</code></pre>

<p>Where include_before should be true if the code needs to be added before the
Base class declaration and false if it should be after.</p>

<p>and</p>

<pre><code class="language-ruby">add_base_implementation_code(code, include_before)
</code></pre>

<h2 id="different-kind-of-steps-during-generation">Different Kind of Steps during Generation</h2>

<p>There are three different hook available which plugins could use.
The above listed <code>pre_generation_hook</code> makes it able to add code
to existing base-methods or register new base methods for generation.</p>

<p>The <code>generation_hook</code> is the normal use-case for plugins. In this generation step
regular ports or methods can be created, but no new code can be added to existing base-members.</p>

<p>The <code>post_generation_hook</code> is only intended to read the finally generated task-context.
No modifications on the TaskContext or the generation should be done at this step. This hook
is useful to extract information during compilation time.</p>

<h2 id="adding-user-defined-classes-to-generation">Adding user-defined classes to generation</h2>

<p>Plugins that want to register additional Classes during compilation
could register a subfolders for the Cmake build system. These subfolders get
added to the compilation unit. The plugin developer has to make sure that the folder
and a CMakeLists.txt within it has been generated during one of the above listed hooks.
If this classes should be installed or linked against other parts, regular CMake commands must
be used. The subfolders names must be export as a enumerable of string by the
<code>each_auto_gen_source_directory</code> method of the plugin, example:</p>

<pre><code class="language-ruby">def each_auto_gen_source_directory(&amp;block)
  ['src1','src2'].each(&amp;block)
end
</code></pre>


</div>
</div>
</div>
