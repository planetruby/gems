# Glimmer DSL for Tk - MRI Ruby Desktop Development GUI Library

## What is Glimmer DSL for Tk?

[Tcl/Tk](https://www.tcl.tk/) has evolved into a practical desktop GUI toolkit due to gaining truely native looking widgets on Mac, Windows, and Linux in [Tk version 8.5](https://www.tcl.tk/software/tcltk/8.5.html#:~:text=Highlights%20of%20Tk%208.5&text=Font%20rendering%3A%20Now%20uses%20anti,and%20window%20layout%2C%20and%20more.).

Additionally, [Ruby](https://www.ruby-lang.org/en/) 3.0 Ractor (formerly known as [Guilds](https://olivierlacan.com/posts/concurrency-in-ruby-3-with-guilds/)) supports truly parallel multi-threading, making both [MRI](https://github.com/ruby/ruby) and [Tk](https://www.tcl.tk/) finally viable for support in [Glimmer](https://github.com/AndyObtiva/glimmer) (DSL Framework) as an alternative to [Glimmer DSL for SWT](https://github.com/AndyObtiva/gems/blob/glimmer/_drafts/glimmer-dsl-swt.md) (JRuby Desktop Development GUI Library).

The trade-off is that while [SWT](https://www.eclipse.org/swt/) provides a plethora of high quality reusable widgets for the Enterprise (such as [Nebula](https://www.eclipse.org/nebula/)), [Tk](https://www.tcl.tk/) enables very fast app startup time and a small memory footprint via [MRI Ruby](https://www.ruby-lang.org/en/).

## Goals

[Glimmer DSL for Tk](https://github.com/AndyObtiva/glimmer-dsl-tk) aims to provide a DSL similar to the [Glimmer DSL for SWT](https://github.com/AndyObtiva/gems/blob/glimmer/_drafts/glimmer-dsl-swt.md) to enable more productive desktop development in Ruby with:
- Declarative DSL syntax that visually maps to the GUI widget hierarchy
- Convention over configuration via smart defaults and automation of low-level details
- Requiring the least amount of syntax possible to build GUI
- Bidirectional Data-Binding to declaratively wire and automatically synchronize GUI with Business Models
- Custom Widget support
- Scaffolding for new custom widgets, apps, and gems
- Native-Executable packaging on Mac, Windows, and Linux

## Girb (Glimmer IRB)

You can run the girb command:

girb

This gives you irb with the [glimmer-dsl-tk](https://rubygems.org/gems/glimmer-dsl-tk) gem loaded and the `Glimmer` module mixed into the main object for easy experimentation with GUI.

## Glimmer GUI DSL Concepts

The Glimmer GUI DSL provides a declarative syntax for [Tk](https://www.tcl.tk/) that:
- Supports smart defaults (e.g. grid layout on most widgets)
- Automates wiring of widgets (e.g. nesting a label under a toplevel root or adding a frame to a notebook)
- Hides lower-level details (e.g. main loop is started automatically when opening a window)
- Nests widgets according to their visual hierarchy
- Requires the minimum amount of syntax needed to describe an app's GUI

The Glimmer GUI DSL follows these simple concepts in mapping from Tk syntax:
- **Widget Keyword**: Any Tk widget (e.g. `Tk::Tile::Label`) or toplevel window (e.g. `TkRoot`) may be declared by its lower-case underscored name without the namespace (e.g. `label` or `root`). This is called a keyword and is represented in the Glimmer GUI DSL by a Ruby method behind the scenes.
- **Args**: Any keyword method may optionally take arguments surrounded by parentheses (e.g. a `frame` nested under a `notebook` may receive tab options like `frame(text: 'Users')`, which gets used behind the scenes by Tk code such as `notebook.add tab, text: 'Users'`)
- **Content/Options Block**: Any keyword may optionally be followed by a Ruby curly-brace block containing nested widgets (content) and attributes (options). Attributes are simply Tk option keywords followed by arguments and no block (e.g. `title 'Hello, World!'` under a `root`)

Example of an app written in [Tk](https://www.tcl.tk/) imperative syntax:

```ruby
root = TkRoot.new
root.title = 'Hello, Tab!'

notebook = ::Tk::Tile::Notebook.new(root).grid

tab1 = ::Tk::Tile::Frame.new(notebook).grid
notebook.add tab1, text: 'English'
label1 = ::Tk::Tile::Label.new(tab1).grid
label1.text = 'Hello, World!'

tab2 = ::Tk::Tile::Frame.new(notebook).grid
notebook.add tab2, text: 'French'
label2 = ::Tk::Tile::Label.new(tab2).grid
label2.text = 'Bonjour, Univers!'

root.mainloop
```

Example of the same app written in [Glimmer](https://github.com/AndyObtiva/glimmer) declarative syntax:

```ruby
root {      
  title 'Hello, Tab!'
   
  notebook {
    frame(text: 'English') {
      label {
        text 'Hello, World!'
      }
    }
     
    frame(text: 'French') {
      label {
        text 'Bonjour, Univers!'
      }
    }
  }
}.open
```

## Bidirectional Data-Binding

Glimmer supports bidirectional data-binding via the `bind` keyword, which takes a model and an attribute.

### Combo Data-Binding

Example:

This assumes a `Person` model with a `country` attribute representing their current country and a `country_options` attribute representing available options for the country attribute.

```ruby
  combobox {
    state 'readonly'       
    text bind(person, :country)
  }
```

That code sets the `values` of the `combobox` to the `country_options` property on the `person` model (data-binding attribute + "_options" by convention).
It also binds the `text` selection of the `combobox` to the `country` property on the `person` model.

It automatically handles all the Tk plumbing behind the scenes, such as using `TkVariable` and setting `combobox` `values` from `person.country_options` by convention (attribute_name + "_options").

More details can be found in the [Hello, Combo!](https://github.com/AndyObtiva/glimmer-dsl-tk#hello-combo) sample below.

### List Single Selection Data-Binding

Tk does not support a native themed listbox, so Glimmer implements its own `list` widget on top of `Tk::Tile::Treeview`. It is set to single selection via selectmode 'browse'.

Example:

This assumes a `Person` model with a `country` attribute representing their current country and a `country_options` attribute representing available options for the country attribute.

```ruby
  list {
    selectmode 'browse'       
    text bind(person, :country)
  }
```

That code binds the `items` text of the `list` to the `country_options` property on the `person` model (data-binding attribute + "_options" by convention).
It also binds the `selection` text of the `list` to the `country` property on the `person` model.

It automatically handles all the Tk plumbing behind the scenes.

More details can be found in the [Hello, List Single Selection!](https://github.com/AndyObtiva/glimmer-dsl-tk#hello-list-single-selection) sample below.

### List Multi Selection Data-Binding

Tk does not support a native themed listbox, so Glimmer implements its own `list` widget on top of `Tk::Tile::Treeview`. It is set to multi selection by default.

Example:

This assumes a `Person` model with a `provinces` attribute representing their current country and a `provinces_options` attribute representing available options for the provinces attribute.

```ruby
  list {
    text bind(person, :provinces)
  }
```

That code binds the `items` text of the `list` to the `provinces_options` property on the `person` model (data-binding attribute + "_options" by convention).
It also binds the `selection` text of the `list` to the `provinces` property on the `person` model.

It automatically handles all the Tk plumbing behind the scenes.

More details can be found in the [Hello, List Multi Selection!](https://github.com/AndyObtiva/glimmer-dsl-tk#hello-list-multi-selection) sample below.

### Label Data-Binding

Example:

This assumes a `Person` model with a `country` attribute.

```ruby
  label {
    text bind(person, :country)
  }
```

That code binds the `textvariable` value of the `label` to the `country` property on the `person` model.

It automatically handles all the Tk plumbing behind the scenes.

More details can be found in the [Hello, Computed!](https://github.com/AndyObtiva/glimmer-dsl-tk#hello-computed) sample below.

### Entry Data-Binding

Example:

This assumes a `Person` model with a `country` attribute.

```ruby
  entry {
    text bind(person, :country)
  }
```

That code binds the `textvariable` value of the `entry` to the `country` property on the `person` model.

It automatically handles all the Tk plumbing behind the scenes.

More details can be found in the [Hello, Computed!](https://github.com/AndyObtiva/glimmer-dsl-tk#hello-computed) sample below.

## Command Observer

Buttons can set a `command` option to trigger when the user clicks the button. This may be done with the `command` keyword, passing in a block directly (no need for `proc` as per Tk)

Example:

```ruby
  button {
    text "Reset Selection"
    command {
      person.reset_country
    }
  }
```

This resets the person country.

More details can be found in the [Hello, Combo!](https://github.com/AndyObtiva/glimmer-dsl-tk#hello-combo) sample below.

## Samples

##### Hello, World!

Glimmer code (from [samples/hello/hello_world.rb](https://github.com/AndyObtiva/glimmer-dsl-tk/blob/master/samples/hello/hello_world.rb)):

```ruby
include Glimmer

root {
  label {
    text 'Hello, World!'
  }
}.open
```

Run (with the [glimmer-dsl-tk](https://rubygems.org/gems/glimmer-dsl-tk) gem installed):

```
ruby -r glimmer-dsl-tk -e "require '../samples/hello/hello_world.rb'"
```

Glimmer app:

![glimmer dsl tk screenshot sample hello world](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-tk/master/images/glimmer-dsl-tk-screenshot-sample-hello-world.png)

### Hello, Computed!

Glimmer code (from [samples/hello/hello_computed.rb](https://github.com/AndyObtiva/glimmer-dsl-tk/blob/master/samples/hello/hello_computed.rb)):

```ruby
# ... more code precedes
    root {
      title 'Hello, Computed!'
      
      frame {        
        grid column: 0, row: 0, padx: 5, pady: 5
        
        label {
          grid column: 0, row: 0, sticky: 'w'
          text 'First Name: '
        }
        entry {
          grid column: 1, row: 0
          width 15
          text bind(@contact, :first_name)
        }
        
        label {
          grid column: 0, row: 1, sticky: 'w'
          text 'Last Name: '
        }
        entry {
          grid column: 1, row: 1
          width 15
          text bind(@contact, :last_name)
        }
        
        label {
          grid column: 0, row: 2, sticky: 'w'
          text 'Year of Birth: '
        }
        entry {
          grid column: 1, row: 2
          width 15
          text bind(@contact, :year_of_birth)
        }
        
        label {
          grid column: 0, row: 3, sticky: 'w'
          text 'Name: '
        }
        label {
          grid column: 1, row: 3, sticky: 'w'
          text bind(@contact, :name, computed_by: [:first_name, :last_name])
        }
        
        label {
          grid column: 0, row: 4, sticky: 'w'
          text 'Age: '
        }
        label {
          grid column: 1, row: 4, sticky: 'w'
          text bind(@contact, :age, on_write: :to_i, computed_by: [:year_of_birth])
        }
      }
    }.open
# ... more code follows
```

Run (with the [glimmer-dsl-tk](https://rubygems.org/gems/glimmer-dsl-tk) gem installed):

```
ruby -r glimmer-dsl-tk -e "require '../samples/hello/hello_computed.rb'"
```

Glimmer app:

![glimmer dsl tk screenshot sample hello computed](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-tk/master/images/glimmer-dsl-tk-screenshot-sample-hello-computed.png)

## Find Out More

- home :: https://github.com/AndyObtiva/glimmer-dsl-tk
- gem :: https://rubygems.org/gems/glimmer-dsl-tk
- rdoc :: https://rubydoc.info/gems/glimmer-dsl-tk
- blog :: http://andymaleh.blogspot.com/search/label/Glimmer
