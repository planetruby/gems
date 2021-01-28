# Day 3 - glimmer-dsl-tk Gem - Script Widgets the Declarative Way (Say What, Not How) - Tk - The Best-Kept Secret and Evergreen Classic Now in v8.5 with Native Look 'n' Feel on Mac, Windows, and Linux


Written by {% avatar AndyObtiva %} [Andy Maleh](https://github.com/AndyObtiva)

_Software Engineering Expert from Montreal, Quebec. Creator of [Glimmer](https://github.com/AndyObtiva/glimmer) and [Abstract Feature Branch](https://github.com/AndyObtiva/abstract_feature_branch). Speaker at RailsConf, RubyConf, AgileConf, EclipseCon, EclipseWorld. Master in Software Engineering, DePaul University, Chicago. Blogs at [Code Mastery Takes Commitment To Bold Coding Adventures](http://andymaleh.blogspot.com/). Snowboarder and Drummer._



## What is Tk?

[Tcl's Tk](https://www.tcl.tk/) has evolved into a practical desktop graphical user interface toolkit due to gaining truely native looking widgets on Mac, Windows, and Linux in [Tk version 8.5](https://www.tcl.tk/software/tcltk/8.5.html#:~:text=Highlights%20of%20Tk%208.5&text=Font%20rendering%3A%20Now%20uses%20anti,and%20window%20layout%2C%20and%20more.).

Additionally, ruby 3.0 ractor (formerly known as guilds) supports truly parallel multi-threading, making both classic ruby and Tk finally viable for support in Glimmer (a language construction kit) as an alternative to Glimmer for the Standard Widget Toolkit (SWT)
running on the java virtual machine (JVM).

The trade-off is that while the Standard Widget Toolkit (SWT) from Eclipse provides a plethora of high quality reusable widgets for the Enterprise (such as Nebula), Tk enables very fast app startup time and a small memory footprint via classic ruby.



## Goals

[Glimmer for Tk](https://github.com/AndyObtiva/glimmer-dsl-tk) aims to provide a domain-specific language similar to the Glimmer for the Standard Widget Toolkit (SWT) to enable more productive desktop development in ruby with:

- Declarative syntax that visually maps to the widget hierarchy
- Convention over configuration via smart defaults and automation of low-level details
- Requiring the least amount of syntax possible to build graphical user interfaces (GUIs)
- Bidirectional two-way data-binding to declaratively wire and automatically synchronize widgets with business models
- Custom widget support
- Scaffolding for new custom widgets, apps, and gems
- Native-executable packaging on Mac, Windows, and Linux



## Girb - Glimmer Interactive Ruby (IRB)

You can run the girb command:

```
girb
```

This gives you irb with the glimmer-dsl-tk gem loaded
and the `Glimmer` module mixed into the main object for easy experimentation with scripting widgets.



## Glimmer Scripting Domain-Specific Language (DSL) Concepts

The Glimmer script language provides a declarative syntax for Tk that:
- Supports smart defaults (e.g. grid layout on most widgets)
- Automates wiring of widgets (e.g. nesting a label under a toplevel root or adding a frame to a notebook)
- Hides lower-level details (e.g. main loop is started automatically when opening a window)
- Nests widgets according to their visual hierarchy
- Requires the minimum amount of syntax needed to describe an app's graphical user interface (GUI)


The Glimmer script follows these simple concepts in mapping from Tk syntax:
- **Widget Keyword**: Any Tk widget (e.g. `Tk::Tile::Label`) or toplevel window (e.g. `TkRoot`) may be declared by its lower-case underscored name without the namespace (e.g. `label` or `root`). This is called a keyword and is represented in the Glimmer script by a method behind the scenes.
- **Args**: Any keyword method may optionally take arguments surrounded by parentheses (e.g. a `frame` nested under a `notebook` may receive tab options like `frame(text: 'Users')`, which gets used behind the scenes by Tk code such as `notebook.add tab, text: 'Users'`)
- **Content/Options Block**: Any keyword may optionally be followed by a curly-brace block containing nested widgets (content) and attributes (options). Attributes are simply Tk option keywords followed by arguments and no block (e.g. `title 'Hello, World!'` under a `root`)

Example of an app written in Tk imperative ("say how") syntax:

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

Example of the same app written in Glimmer declarative ("say what") syntax:

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


## Two-Way Data-Binding

Glimmer supports bidirectional two-way data-binding via the `bind` keyword, which takes a model and an attribute.

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

More details can be found in the [Hello, Combo!](https://github.com/AndyObtiva/glimmer-dsl-tk#hello-combo) sample in the README..


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

More details can be found in the [Hello, List Single Selection!](https://github.com/AndyObtiva/glimmer-dsl-tk#hello-list-single-selection) in the README.


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

More details can be found in the [Hello, List Multi Selection!](https://github.com/AndyObtiva/glimmer-dsl-tk#hello-list-multi-selection) sample in the README.


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

More details can be found in the Hello, Computed! sample below.


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

More details can be found in the Hello, Computed! sample below.


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

More details can be found in the [Hello, Combo!](https://github.com/AndyObtiva/glimmer-dsl-tk#hello-combo) sample in the README.



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

Run (with the glimmer-dsl-tk gem installed):

```
ruby -r glimmer-dsl-tk -e "require '../samples/hello/hello_world.rb'"
```

Glimmer app:

![](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-tk/master/images/glimmer-dsl-tk-screenshot-sample-hello-world.png)


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

Run (with the glimmer-dsl-tk gem installed):

```
ruby -r glimmer-dsl-tk -e "require '../samples/hello/hello_computed.rb'"
```

Glimmer app:

![](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-tk/master/images/glimmer-dsl-tk-screenshot-sample-hello-computed.png)




## Find Out More

### References

- Home :: [github.com/AndyObtiva/glimmer-dsl-tk](https://github.com/AndyObtiva/glimmer-dsl-tk)
- Gem :: [glimmer-dsl-tk](https://rubygems.org/gems/glimmer-dsl-tk)
- Docs :: [glimmer-dsl-tk](https://rubydoc.info/gems/glimmer-dsl-tk)
- Blog :: [Glimmer Tetris in One Day! and Many More Glimmer Articles](http://andymaleh.blogspot.com/search/label/Glimmer)
- Video :: [Whatever Happened to Desktop Development in Ruby?, MountainWest RubyConf 2011](https://confreaks.tv/videos/mwrc2011-whatever-happened-to-desktop-development-in-ruby)

