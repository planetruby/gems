# [<img src="https://raw.githubusercontent.com/AndyObtiva/glimmer/master/images/glimmer-logo-hi-res.png" height=85 style="position: relative; top: 20px;" />](https://github.com/AndyObtiva/glimmer) Glimmer DSL for SWT - JRuby Desktop Development GUI Framework

## What is Glimmer DSL for SWT?

[Glimmer DSL for SWT](https://github.com/AndyObtiva/glimmer-dsl-swt) is a native-GUI cross-platform desktop development library written in [JRuby](https://www.jruby.org/), an OS-threaded faster JVM version of [Ruby](https://www.ruby-lang.org/en/). [Glimmer](https://github.com/AndyObtiva/glimmer)'s main innovation is a declarative [Ruby DSL](https://github.com/AndyObtiva/glimmer-dsl-swt#glimmer-dsl-syntax) that enables productive and efficient authoring of desktop application user-interfaces by relying on the robust [Eclipse SWT library](https://www.eclipse.org/swt/). [Glimmer](https://rubygems.org/gems/glimmer) additionally innovates by having built-in [data-binding](https://github.com/AndyObtiva/glimmer-dsl-swt#data-binding) support, which greatly facilitates synchronizing the GUI with domain models, thus achieving true decoupling of object oriented components and enabling developers to solve business problems (test-first) without worrying about GUI concerns, or alternatively drive development GUI-first, and then write clean business models (test-first) afterwards. Not only does Glimmer provide a large set of GUI [widgets](https://github.com/AndyObtiva/glimmer-dsl-swt#widgets), but it also supports drawing Canvas Graphics like [Shapes](https://github.com/AndyObtiva/glimmer-dsl-swt#canvas-shape-dsl) and [Animations](https://github.com/AndyObtiva/glimmer-dsl-swt#canvas-animation-dsl). To get started quickly, [Glimmer](https://rubygems.org/gems/glimmer) s [scaffolding](https://github.com/AndyObtiva/glimmer-dsl-swt#scaffolding) options for [Apps](https://github.com/AndyObtiva/glimmer-dsl-swt#in-production), [Gems](https://github.com/AndyObtiva/glimmer-dsl-swt#custom-shell-gem), and [Custom Widgets](https://github.com/AndyObtiva/glimmer-dsl-swt#custom-widgets). [Glimmer](https://rubygems.org/gems/glimmer) also includes native-executable [packaging](https://github.com/AndyObtiva/glimmer-dsl-swt#packaging--distribution) support, sorely lacking in other libraries, thus enabling the delivery of desktop apps written in [Ruby](https://www.ruby-lang.org/en/) as truly native DMG/PKG/APP files on the [Mac](https://www.apple.com/ca/macos) + [App Store](https://developer.apple.com/macos/distribution/), MSI/EXE files on [Windows](https://www.microsoft.com/en-ca/windows), and [Gem Packaged Shell Scripts](https://github.com/AndyObtiva/glimmer-dsl-swt#custom-shell-gem) on [Linux](https://www.linux.org/).

## Background

[Ruby](https://www.ruby-lang.org) is a dynamically-typed object-oriented language, which provides great productivity gains due to its powerful expressive syntax and dynamic nature. While it is proven by the [Ruby](https://www.ruby-lang.org) on Rails framework for web development, it currently lacks a robust platform-independent framework for building desktop applications. Given that Java libraries can now be utilized in Ruby code through JRuby, Eclipse technologies, such as [SWT](https://www.eclipse.org/swt/), JFace, and RCP can help fill the gap of desktop application development with Ruby.

## In Production

The following production apps have been built with [Glimmer DSL for SWT](https://github.com/AndyObtiva/glimmer-dsl-swt).

### Math Bowling

[<img alt="Math Bowling Logo" src="https://raw.githubusercontent.com/AndyObtiva/MathBowling/master/images/math-bowling-logo.png" width="40" />Math Bowling](https://github.com/AndyObtiva/MathBowling): an educational math game for elementary level kids

[![Math Bowling App Screenshot](https://raw.githubusercontent.com/AndyObtiva/MathBowling/master/Math-Bowling-Screenshot.png)](https://github.com/AndyObtiva/MathBowling)

### Are We There Yet?

[<img alt="Are We There Yet Logo" src="https://raw.githubusercontent.com/AndyObtiva/are-we-there-yet/master/are-we-there-yet-logo.svg" width="40" />Are We There Yet?](https://github.com/AndyObtiva/are-we-there-yet): A tool that helps you learn when your small projects will finish

[![Are We There Yet? App Screenshot](https://raw.githubusercontent.com/AndyObtiva/are-we-there-yet/master/are-we-there-yet-screenshot-windows.png)](https://github.com/AndyObtiva/are-we-there-yet)

### Garderie Rainbow Daily Agenda

[<img alt="Garderie Rainbow Daily Agenda Logo" src="https://github.com/AndyObtiva/garderie_rainbow_daily_agenda/raw/master/images/garderie_rainbow_daily_agenda_logo.png" width="40" />Garderie Rainbow Daily Agenda](https://github.com/AndyObtiva/garderie_rainbow_daily_agenda): A child nursery daily agenda reporting desktop app

[![Garderie Rainbow](https://raw.githubusercontent.com/AndyObtiva/garderie_rainbow_daily_agenda/master/images/garderie_rainbow_daily_agenda_screenshot.png)](https://github.com/AndyObtiva/garderie_rainbow_daily_agenda)

## Samples

Glimmer DSL for SWT has [many samples](https://github.com/AndyObtiva/glimmer-dsl-swt#samples), including the ones mentioned below.

### Hello, World!

Glimmer code (from [samples/hello/hello_world.rb](https://github.com/AndyObtiva/glimmer-dsl-swt/blob/master/samples/hello/hello_world.rb)):
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

This sample demonstrates the use of [table](https://github.com/AndyObtiva/glimmer-dsl-swt#table) widget in Glimmer, including data-binding, multi-type editing, sorting, and filtering.

Code:

[samples/hello/hello_table.rb](https://github.com/AndyObtiva/glimmer-dsl-swt/blob/master/samples/hello/hello_table.rb)

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

Hello, Table! Editing Game Time

![Hello Table](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-swt/master/images/glimmer-hello-table-editing-game-time.png)

Hello, Table! Editing Home Team

![Hello Table](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-swt/master/images/glimmer-hello-table-editing-home-team.png)

Hello, Table! Sorted Game Date Ascending

![Hello Table](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-swt/master/images/glimmer-hello-table-sorted-game-date-ascending.png)

Hello, Table! Sorted Game Date Descending

![Hello Table](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-swt/master/images/glimmer-hello-table-sorted-game-date-descending.png)

Hello, Table! Playoff Type Combo

![Hello Table](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-swt/master/images/glimmer-hello-table-playoff-type-combo.png)

Hello, Table! Playoff Type Changed

![Hello Table](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-swt/master/images/glimmer-hello-table-playoff-type-changed.png)

Hello, Table! Game Booked

![Hello Table](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-swt/master/images/glimmer-hello-table-game-booked.png)

Hello, Table! Context Menu

![Hello Table](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-swt/master/images/glimmer-hello-table-context-menu.png)

## What Else Is Special?

- [Glimmer Command TUI](https://github.com/AndyObtiva/glimmer-dsl-swt#glimmer-command)
- [Scaffolding](https://github.com/AndyObtiva/glimmer-dsl-swt#scaffolding)
- [Girb (Glimmer IRB)](https://github.com/AndyObtiva/glimmer-dsl-swt#girb-glimmer-irb-command)
- [Canvas Shape DSL](https://github.com/AndyObtiva/glimmer-dsl-swt#canvas-shape-dsl)
- [Canvas Animation DSL](https://github.com/AndyObtiva/glimmer-dsl-swt#canvas-animation-dsl)
- [Native Executable Packaging (e.g. DMG or MSI)](https://github.com/AndyObtiva/glimmer-dsl-swt#packaging--distribution)
- [Webify with Rails without Changing a Line of Code by using Glimmer DSL for Opal](https://github.com/AndyObtiva/gems/blob/glimmer/_drafts/glimmer-dsl-opal.md)

## Find Out More

- home :: https://github.com/AndyObtiva/glimmer-dsl-swt
- gem :: https://rubygems.org/gems/glimmer-dsl-swt
- rdoc :: https://rubydoc.info/gems/glimmer-dsl-swt
- blog :: http://andymaleh.blogspot.com/search/label/Glimmer
- books 
  - JRuby Cookbook by Justin Edelson & Henry Liu :: http://shop.oreilly.com/product/9780596519650.do
- articles ::
  - InfoQ :: http://www.infoq.com/news/2008/02/glimmer-jruby-swt
  - DZone :: https://dzone.com/articles/an-introduction-glimmer
- presentations ::
  - RubyConf 2008 :: https://confreaks.tv/videos/rubyconf2008-desktop-development-with-glimmer
  - MountainWest RubyConf 2011 :: https://confreaks.tv/videos/mwrc2011-whatever-happened-to-desktop-development-in-ruby
