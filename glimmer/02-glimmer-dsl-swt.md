# Day 2 - glimmer-dsl-swt Gem - Script Standard Widgets from the Eclipse Toolkit with Two-Way Data Binding (incl. Tables with Sorting, Filtering, Multi-Type Editing, and More) - Package Up and Ship Desktop Apps for Mac, Windows 'n' Linux


Written by {% avatar AndyObtiva %} [Andy Maleh](https://github.com/AndyObtiva)

_Software Engineering Expert from Montreal, Quebec. Creator of [Glimmer](https://github.com/AndyObtiva/glimmer) and [Abstract Feature Branch](https://github.com/AndyObtiva/abstract_feature_branch). Speaker at RailsConf, RubyConf, AgileConf, EclipseCon, EclipseWorld. Master in Software Engineering, DePaul University, Chicago. Blogs at [Code Mastery Takes Commitment To Bold Coding Adventures](http://andymaleh.blogspot.com/). Snowboarder and Drummer._




## What's the Standard Widget Toolkit (SWT)?


[Glimmer for the Standard Widget Toolkit (SWT)](https://github.com/AndyObtiva/glimmer-dsl-swt) is a domain-specifc language (DSL) for scripting native cross-platform desktop apps in ruby running on the OS-threaded faster Java Virtual Machine (JVM).

Glimmer's main innovation is a declarative language that enables productive and efficient scripting of desktop application user-interfaces by relying on the robust
[Standard Widgets Toolkit](https://www.eclipse.org/swt/) from the Eclipse project.
Glimmer additionally innovates by having built-in two-way data-binding support, which greatly helps synchronizing the widgets with domain models, thus achieving true decoupling of object oriented components and enabling developers to solve business problems (test-first) without worrying about GUI concerns, or alternatively drive development GUI-first, and then write clean business models (test-first) afterwards. Not only does Glimmer provide a large set of widgets, but it also supports drawing canvas graphics like shapes
and animations.

To get started quickly, Glimmer's scaffolding options for apps, gems,
and custom widgets. Glimmer also includes native-executable packaging
support, sorely lacking in other libraries, thus enabling the delivery of desktop apps written in ruby as truly native DMG/PKG/APP files on the Mac + App Store,
MSI/EXE files on Windows, and Gem Packaged Shell Scripts on Linux.


## Getting Started - Hello, Glimmer!

Glimmer for the Standard Widget Toolkit (SWT) has many samples,
including the ones mentioned below.


### Hello, World!

Glimmer code (from [`samples/hello/hello_world.rb`](https://github.com/AndyObtiva/glimmer-dsl-swt/blob/master/samples/hello/hello_world.rb)):

```ruby
include Glimmer

shell {
  text "Glimmer"
  label {
    text "Hello, World!"
  }
}.open
```

Run via `glimmer samples` or directly:

```
glimmer samples/hello/hello_world.rb
```

Glimmer app:

![Hello World](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-swt/master/images/glimmer-hello-world.png)



### Hello, Table!

This sample demonstrates the use of the table widget in Glimmer, including data-binding, multi-type editing, sorting, and filtering.

Code:

[`samples/hello/hello_table.rb`](https://github.com/AndyObtiva/glimmer-dsl-swt/blob/master/samples/hello/hello_table.rb)

```ruby
# more code comes before
shell {
  grid_layout

  text 'Hello, Table!'

  label {
    layout_data :center, :center, true, false

    text 'Baseball Playoff Schedule'
    font height: 30, style: :bold
  }

  combo(:read_only) {
    layout_data :center, :center, true, false
    selection bind(BaseballGame, :playoff_type)
    font height: 16
  }

  table(:editable) { |table_proxy|
    layout_data :fill, :fill, true, true

    table_column {
      text 'Game Date'
      width 150
      sort_property :date # ensure sorting by real date value (not `game_date` string specified in items below)
      editor :date_drop_down, property: :date_time
    }
    table_column {
      text 'Game Time'
      width 150
      sort_property :time # ensure sorting by real time value (not `game_time` string specified in items below)
      editor :time, property: :date_time
    }
    table_column {
      text 'Ballpark'
      width 180
      editor :none
    }
    table_column {
      text 'Home Team'
      width 150
      editor :combo, :read_only # read_only is simply an SWT style passed to combo widget
    }
    table_column {
      text 'Away Team'
      width 150
      editor :combo, :read_only # read_only is simply an SWT style passed to combo widget
    }
    table_column {
      text 'Promotion'
      width 150
      # default text editor is used here
    }

    # Data-bind table items (rows) to a model collection property, specifying column properties ordering per nested model
    items bind(BaseballGame, :schedule), column_properties(:game_date, :game_time, :ballpark, :home_team, :away_team, :promotion)

    # Data-bind table selection
    selection bind(BaseballGame, :selected_game)

    # Default initial sort property
    sort_property :date

    # Sort by these additional properties after handling sort by the column the user clicked
    additional_sort_properties :date, :time, :home_team, :away_team, :ballpark, :promotion

    menu {
      menu_item {
        text 'Book'

        on_widget_selected {
          book_selected_game
        }
      }
    }
  }

  button {
    text 'Book Selected Game'
    layout_data :center, :center, true, false
    font height: 16
    enabled bind(BaseballGame, :selected_game)

    on_widget_selected {
      book_selected_game
    }
  }
}.open
# more code comes after
```

Hello, Table!

![Hello Table](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-swt/master/images/glimmer-hello-table.png)

Hello, Table! Editing Game Date

![Hello Table](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-swt/master/images/glimmer-hello-table-editing-game-date.png)



## What Else Is Special?

- [Glimmer Command TUI](https://github.com/AndyObtiva/glimmer-dsl-swt#glimmer-command)
- [Scaffolding](https://github.com/AndyObtiva/glimmer-dsl-swt#scaffolding)
- [Girb (Glimmer IRB)](https://github.com/AndyObtiva/glimmer-dsl-swt#girb-glimmer-irb-command)
- [Canvas Shape DSL](https://github.com/AndyObtiva/glimmer-dsl-swt#canvas-shape-dsl)
- [Canvas Animation DSL](https://github.com/AndyObtiva/glimmer-dsl-swt#canvas-animation-dsl)
- [Native Executable Packaging (e.g. DMG or MSI)](https://github.com/AndyObtiva/glimmer-dsl-swt#packaging--distribution)
- [Webify with Rails without Changing a Line of Code by using Glimmer DSL for Opal](https://github.com/AndyObtiva/gems/blob/glimmer/_drafts/glimmer-dsl-opal.md)


## In Production

The following production apps have been built with Glimmer
for the Standard Widget Toolkit (SWT).


### Are We There Yet?

Are We There Yet? - A tool that helps you learn when your small projects will finish -
([Source](https://github.com/AndyObtiva/are-we-there-yet))

[![Are We There Yet? App Screenshot](https://raw.githubusercontent.com/AndyObtiva/are-we-there-yet/master/are-we-there-yet-screenshot-windows.png)](https://github.com/AndyObtiva/are-we-there-yet)


### Math Bowling

Math Bowling - an educational math game for elementary level kids -
([Source](https://github.com/AndyObtiva/MathBowling))

[![Math Bowling App Screenshot](https://raw.githubusercontent.com/AndyObtiva/MathBowling/master/Math-Bowling-Screenshot.png)](https://github.com/AndyObtiva/MathBowling)



## Find Out More

### References

- Home :: [github.com/AndyObtiva/glimmer-dsl-swt](https://github.com/AndyObtiva/glimmer-dsl-swt)
- Gem :: [glimmer-dsl-swt](https://rubygems.org/gems/glimmer-dsl-swt)
- Docs :: [glimmer-dsl-swt](https://rubydoc.info/gems/glimmer-dsl-swt)
- Blog :: [Glimmer Tetris in One Day! and Many More Glimmer Articles](http://andymaleh.blogspot.com/search/label/Glimmer)
- Video :: [Whatever Happened to Desktop Development in Ruby?, MountainWest RubyConf 2011](https://confreaks.tv/videos/mwrc2011-whatever-happened-to-desktop-development-in-ruby)

