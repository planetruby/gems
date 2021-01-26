# Day 1 - glimmer Gem - Make Desktop Apps Shine Using a Script with Two-Way Data Binding - Inside the Domain-Specific Language Engine / Construction Kit - Beware of Imitators! The Original Since 2007



Written by {% avatar AndyObtiva %} [Andy Maleh](https://github.com/AndyObtiva)

_Software Engineering Expert from Montreal, Quebec. Creator of [Glimmer](https://github.com/AndyObtiva/glimmer) and [Abstract Feature Branch](https://github.com/AndyObtiva/abstract_feature_branch). Speaker at RailsConf, RubyConf, AgileConf, EclipseCon, EclipseWorld. Master in Software Engineering, DePaul University, Chicago. Blogs at [Code Mastery Takes Commitment To Bold Coding Adventures](http://andymaleh.blogspot.com/). Snowboarder and Drummer._




## What is Glimmer?

[**Glimmer**](https://rubygems.org/gems/glimmer) started out as a [domain-specific language (DSL) for the Simple Widget Toolkit (SWT)](https://github.com/AndyObtiva/glimmer-dsl-swt) and grew into a domain-specific languge (DSL) engine
supporting scripting multiple graphical user interfaces.
Glimmer's namesake is referring to the glimmer of ruby in graphical user interfaces.


Glimmer consists of:
- Domain-Specific Language (DSL) Engine: enables building embedded internal domain-specific languages (DSLs) (e.g. for GUI, XML, or CSS).
- Data-Binding Machinery: enables synchronizing graphic user interfaces with model attributes two-ways.



## What makes Glimmer special?

Glimmer offers:
- Supports building the tersest most concise domain-specific language syntax.
- Maximum readability and maintainability.
- No extra unnecessary block variables when not needed.
- Domain-specific language blocks are true closures that can conveniently leverage variables from the outside and use standard code in and around. Just code as usual and be happy! No surprising restrictions or strange uses of `instance_exec`/`eval`.
- Domain-specific language syntax is limited to classes that mixin the `Glimmer` module, so the rest of the code is fully safe from namespace pollution.
- Multiple domain-specific languages may be mixed together safely to achieve maximum expressability, composability, and productivity.
- Domain-specific languages are fully configurable, so you may activate and deactivate as per your current needs only.


Glimmer currently supports:
- SWT (Simple Widget Toolkit) for Desktop Apps in Java
- Tk (Toolkit) for Desktop Apps
- Opal for Web Apps and Auto-Webifier of Desktop Apps
- XML (& HTML)
- CSS



## The Glimmer Language Scripting Syntax

Glimmer is fundamentally a domain-specific language engine
and syntax consists mainly of:

- **keywords** (e.g. `table` for a table widget)
- **style/args** (e.g. :multi as in `table(:multi)` for a multi-line selection table widget)
- **content** (e.g. `{ table_column { text 'Name'} }` as in `table(:multi) { table_column { text 'Name'} }` for a multi-line selection table widget with a table column having header text property `'Name'` as content)



The Glimmer engine allows mixing of languages, which comes in handy when doing things like rendering a desktop `browser` widget additionally leveraging the HTML and CSS domain-specific languages for its content.

Domain-specific languages are activated by top-level keywords (expressions denoted as `TopLevelExpression`). For example, the `html` keyword activates the [Glimmer for XML](https://github.com/AndyObtiva/glimmer-dsl-xml) and the `css` keyword activates the [Glimmer for CSS](https://github.com/AndyObtiva/glimmer-dsl-css). Glimmer automatically recognizes top-level keywords in each language and activates the it accordingly. Once done processing a nested top-level keyword, Glimmer switches back to the prior language automatically.

By default, all loaded domain-specific languages (required gems) are enabled.

For example, this shows "Hello, World!" inside a [Glimmer for SWT](https://github.com/AndyObtiva/glimmer-dsl-swt) desktop app `browser` widget using `html` and `css` from [Glimmer for XML](https://github.com/AndyObtiva/glimmer-dsl-xml) and [Glimmer for CSS](https://github.com/AndyObtiva/glimmer-dsl-css):

```ruby
require 'glimmer-dsl-swt'
require 'glimmer-dsl-xml'
require 'glimmer-dsl-css'

include Glimmer

shell {
  minimum_size 130, 130
  @browser = browser {
    text html {
      head {
        meta(name: "viewport", content: "width=device-width, initial-scale=2.0")
        style {
          css {
            h1 {
              background 'yellow'
            }
          }
        }
      }
      body {
        h1 { "Hello, World!" }
      }
    }
  }
}.open
```




## Inside the Domain-Specific Language (DSL) Engine


The Glimmer engine's architecture is based on the following design patterns and data structures:
- **Interpreter Design Pattern**: to define interpretable expressions of language keywords
- **Chain of Responsibility Design Pattern / Queue Data Structure**: to chain expression handlers in order of importance for processing language keywords
- **Adapter Design Pattern**: to adapt expressions into handlers in a chain of responsibility
- **Stack Data Structure**: to handle processing parent/child nesting of language keyword expressions in the correct order


Glimmer's use of the **Interpreter Design Pattern** in processing language scripts is also known as the **Virtual Machine Architectural Style**. After all,  expressions are virtual machine opcodes that process nested keywords stored in a stack. I built Glimmer's original domain-specific language back in 2007 without knowing the **Virtual Machine Architectural Style** (except perhaps as an esoteric technology powering java), but stumbled upon it anyways through following the Gang of Four Design Patterns mentioned above, chiefly the **Interpreter Design Pattern**.

Every keyword in a Glimmer language is represented by an expression that is processed by an `Expression` subclass selected from a chain of expressions (interpreters) pre-configured in a domain-specific language chain of responsibility via `Glimmer::DSL::Engine.add_dynamic_expressions(DSLNameModule, expression_names_array)`.

Expressions are either:
- **Static** (subclass of `StaticExpression`, which is a subclass of `Expression`): if they represent a single pre-identified keyword (e.g. `color` or `display`)
- **Dynamic** (subclass of `Expression`): if they represent keywords calculated on the fly during processing (e.g. an SWT widget like `label` or a random XML element called `folder` representing `<folder></folder>`)

Optionally, expressions can be parent expressions that contain other expressions, and must include the `ParentExpression` mixin module as such.

Additionally, every expression that serves as a top-level entry point into the language must mixin `TopLevelExpression`

Static expressions are optimized in performance since they pre-define methods on the `Glimmer` module matching the static keywords they represent (e.g. `color` causes creating a `Glimmer#color` method for processing `color` expressions) and completely bypass as a result the Glimmer Engine Chain of Responsibility. That said, they must be avoided if the same keyword might occur multiple times, but with different requirements for arguments, block, and parenthood type.

Every `Expression` sublcass must specify two methods at least:
- `can_interpret?(parent, keyword, *args, &block)`: to quickly test if the keyword and arg/block/parent combination qualifies for interpretation by the current `Expression` or to otherwise delegate to the next expression in the chain of responsibility.
- `interpret(parent, keyword, *args, &block)`: to go ahead and interpret a DSL expression that qualified for interpretation

`StaticExpression` sublcasses may skip the `can_interpret?` method since they include a default implementation for it that matches the name of the keyword from the class name by convention. For example, a `color` keyword would have a `ColorExpression` class, so `color` is inferred automatically from class name and used in deciding whether the class can handle a `color` keyword or not.

`ParentExpression` subclasses can optionally override this extra method, which is included by default and simply invokes the parent's passed block to process its children:
- `add_content(parent, &block)`

For example, some parent widgets use their block for other reasons or process their children at very specific times, so they may override that method and disable it, or otherwise call `super` and do additional work.

Example of a dynamic expression:

```ruby
module Glimmer
  module DSL
    module SWT
      class WidgetExpression < Expression
        include ParentExpression

        EXCLUDED_KEYWORDS = %w[shell display tab_item]

        def can_interpret?(parent, keyword, *args, &block)
          !EXCLUDED_KEYWORDS.include?(keyword) and
            parent.respond_to?(:swt_widget) and
            Glimmer::SWT::WidgetProxy.widget_exists?(keyword)
        end

        def interpret(parent, keyword, *args, &block)
          Glimmer::SWT::WidgetProxy.create(keyword, parent, args)
        end

        def add_content(parent, &block)
          super
          parent.post_add_content
        end

      end
    end
  end
end
```

Example of a static expression (does not need `can_interpret?`):

```ruby
module Glimmer
  module DSL
    module Opal
      class ColorExpression < StaticExpression
        include TopLevelExpression

        def interpret(parent, keyword, *args, &block)
          Glimmer::SWT::ColorProxy.new(*args)
        end
      end
    end
  end
end
```

Epressions go into the `glimmer/dsl/{dsl_name}` namespace directory.

Also, every domain-specific language requires a `glimmer/dsl/{dsl_name}/dsl.rb` file, which configures the language into Glimmer via a call to:

```ruby
Glimmer::DSL::Engine.add_dynamic_expressions(DSLNameModule, expression_names_array)
```

Expression names are underscored verions of `Expression` subclass names minus the `_expression` suffix.

For example, here is an SWT configuration:

```ruby
require 'glimmer/launcher'
require Glimmer::Launcher.swt_jar_file
require 'glimmer/dsl/engine'
Dir[File.expand_path('../*_expression.rb', __FILE__)].each {|f| require f}

module Glimmer
  module DSL
    module SWT
      Engine.add_dynamic_expressions(
        SWT,
        %w[
          layout
          widget_listener
          combo_selection_data_binding
          checkbox_group_selection_data_binding
          radio_group_selection_data_binding
          list_selection_data_binding
          tree_items_data_binding
          table_items_data_binding
          data_binding
          cursor
          font
          image
          property
          block_property
          widget
          custom_widget
        ]
      )
    end
  end
end
```

In summary, these are the files needed to author a Glimmer domain-specific language:
- `glimmer/dsl/[dsl_name]/dsl.rb`: requires and adds all dynamic expressions to [dsl_name] Glimmer language
- `glimmer/dsl/[dsl_name]/[expresion_name]_expresion.rb`: needed for every [expresion_name] expression, whether dynamic or static



## Data-Binding Machinery

Data-Binding enables binding graphical user interface properties (like text and color) to model attributes (like name and age).

It relies on the Observer Design Pattern and  Model-View-Presenter (MVP) Architectural Pattern - a variation on Model-View-Cotnroller (MVC).

These are the main classes concerning data-binding:
- `Observer`: Provides general observer support including unique registration and deregistration for cleanup and prevention of memory leaks. Main methods concerned are: `call`, `register` (alias: `observe`), and `unregister` (alias: `unobserve` or `deregister`)
- `Observable`: General super-module for all observables. Main methods concerned are: `add_observer` and `remove_observer`
- `ObservableModel`: Mixin module for any observable model with observable attributes. In addition to `Observable` methods, it has a `notify_observers` method to be called when changes occur. It automatically enhances all attribute setters (ending with `=`) to notify observers on changes. Also, it automatically handles observing array attributes using `ObservableArray` appropriately so they would notify observers upon array mutation changes.
- `ObservableArray`: Mixin module for any observable array collection that automatically handles notifying observers upon performing array mutation operations (e.g. `push` or `delete`)
- `ModelBinding`: a higher-level abstraction that relies on all the other observer/observable classes to support basic data-binding, nested data-binding, and computed data-binding



## Find Out More

### References

- Home :: [github.com/AndyObtiva/glimmer](https://github.com/AndyObtiva/glimmer)
- Gem :: [glimmer]()https://rubygems.org/gems/glimmer
- Docs :: [glimmer](https://rubydoc.info/gems/glimmer)
- Blog :: [Glimmer Tetris in One Day! and Many More Glimmer Articles](http://andymaleh.blogspot.com/search/label/Glimmer)
- Video :: [Whatever Happened to Desktop Development in Ruby?, MountainWest RubyConf 2011](https://confreaks.tv/videos/mwrc2011-whatever-happened-to-desktop-development-in-ruby)

