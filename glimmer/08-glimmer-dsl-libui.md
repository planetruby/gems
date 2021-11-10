# Day 8 - glimmer-dsl-libui Gem - Prerequisite-Free Ruby Desktop Development Library

Written by {% avatar AndyObtiva %} [Andy Maleh](https://github.com/AndyObtiva)

_Software Engineering Expert from Montreal, Quebec. Creator of [Glimmer](https://github.com/AndyObtiva/glimmer) and [Abstract Feature Branch](https://github.com/AndyObtiva/abstract_feature_branch). Speaker at RailsConf, RubyConf, AgileConf, EclipseCon, EclipseWorld. Master in Software Engineering, DePaul University, Chicago. Blogs at [Code Mastery Takes Commitment To Bold Coding Adventures](http://andymaleh.blogspot.com/). Snowboarder and Drummer._




## What is Glimmer DSL for LibUI?

**[Glimmer DSL for LibUI](https://github.com/AndyObtiva/glimmer-dsl-libui)** is a prerequisite-free Ruby desktop development GUI (Graphical User Interface) library. [LibUI](https://github.com/kojix2/LibUI) is a thin [Ruby](https://www.ruby-lang.org/en/) wrapper around [libui](https://github.com/andlabs/libui), a relatively new C GUI library that renders native desktop GUI controls on every platform (similar to [SWT](https://www.eclipse.org/swt/), but without the heavy weight of the [Java Virtual Machine](https://www.java.com/en/)).

[Glimmer DSL for LibUI](https://rubygems.org/gems/glimmer-dsl-libui) is a good convenient choice for small simple applications due to having zero prerequisites beyond the dependencies included in the [Ruby gem](https://rubygems.org/gems/glimmer-dsl-libui). Also, just like [Glimmer DSL for Tk](/glimmer/03-glimmer-dsl-tk.md), its apps start instantly and have a small memory footprint. [LibUI](https://github.com/kojix2/LibUI) is a promising new GUI toolkit that might prove quite worthy in the future.

| Mac | Windows | Linux |
|-----|---------|-------|
|<img src="https://user-images.githubusercontent.com/5798442/103118059-99980f80-46b0-11eb-9d12-324ec4d297c9.png">|<img src="https://user-images.githubusercontent.com/5798442/103118046-900ea780-46b0-11eb-81fc-32626762e4df.png">|<img src="https://user-images.githubusercontent.com/5798442/103118068-a0bf1d80-46b0-11eb-8c5c-3bdcc3dcfb26.png">|




## What makes Glimmer DSL for LibUI special?

No need to pre-install any prerequisites. Just install the gem and have platform-independent native GUI that just works!

[Glimmer DSL for LibUI](https://rubygems.org/gems/glimmer-dsl-libui) aims to provide a DSL (Domain Specific Language) that enables more productive desktop development in Ruby with:
- Declarative DSL syntax that visually maps to the GUI control hierarchy
- Convention over configuration via smart defaults and automation of low-level details
- Requiring the least amount of syntax possible to build GUI
- Custom Keyword support
- Automates wiring of controls (e.g. `button` is automatically set as child of `window`)
- Hides lower-level details (e.g. `LibUI.main` loop is started automatically when triggering `show` on `window`)



## The Glimmer GUI DSL Syntax

The Glimmer GUI DSL follows these simple concepts in mapping from [LibUI](https://github.com/kojix2/LibUI) syntax:
- **Keyword(args)**: [LibUI](https://github.com/kojix2/LibUI) controls may be declared by lower-case underscored name (aka keyword) (e.g. `window` or `button`). Behind the scenes, they are represented by keyword methods that map to corresponding `LibUI.new_keyword` methods receiving args (e.g. `window('hello world', 300, 200, true)`).
- **Content Block** (Properties/Listeners/Controls): Any keyword may be optionally followed by a Ruby curly-brace multi-line content block containing properties (attributes), listeners, and/or nested controls (e.g. `window {title 'hello world'; on_closing {puts 'Bye'}; button('greet')}`). Content block optionally receives one arg representing the control (e.g. `button('greet') {|b| on_clicked { puts b.text}}`)
- **Property**: Control properties may be declared inside keyword blocks with lower-case underscored name followed by property value args (e.g. `title "hello world"` inside `group`). Behind the scenes, properties correspond to `LibUI.control_set_property` methods.
- **Listener**: Control listeners may be declared inside keyword blocks with listener lower-case underscored name beginning with `on_` and receiving required block handler (e.g. `on_clicked {puts 'clicked'}` inside `button`). Optionally, the listener block can receive an arg representing the control (e.g. `on_clicked {|btn| puts btn.text}`). Behind the scenes, listeners correspond to `LibUI.control_on_event` methods.

Example of an app written in Glimmer GUI DSL object-oriented declarative hierarchical syntax:

```ruby
require 'glimmer-dsl-libui'

include Glimmer

window('hello world', 300, 200) {
  button('Button') {
    on_clicked do
      msg_box('Information', 'You clicked the button')
    end
  }
  
  on_closing do
    puts 'Bye Bye'
  end
}.show
```

Mac Screenshots

![glimmer-dsl-libui-mac-basic-button.png](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-libui/master/images/glimmer-dsl-libui-mac-basic-button.png)
![glimmer-dsl-libui-mac-basic-button-msg-box.png](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-libui/master/images/glimmer-dsl-libui-mac-basic-button-msg-box.png)

Windows Screenshots

![glimmer-dsl-libui-windows-basic-button.png](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-libui/master/images/glimmer-dsl-libui-windows-basic-button.png)
![glimmer-dsl-libui-windows-basic-button-msg-box.png](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-libui/master/images/glimmer-dsl-libui-windows-basic-button-msg-box.png)

Linux Screenshots

![glimmer-dsl-libui-linux-basic-button.png](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-libui/master/images/glimmer-dsl-libui-linux-basic-button.png)
![glimmer-dsl-libui-linux-basic-button-msg-box.png](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-libui/master/images/glimmer-dsl-libui-linux-basic-button-msg-box.png)





## Hello, World!


```ruby
require 'glimmer-dsl-libui'

include Glimmer

window('hello world').show
```


Mac Screenshot:

![glimmer-dsl-libui-mac-basic-window.png](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-libui/master/images/glimmer-dsl-libui-mac-basic-window.png)

Windows Screenshot:

![glimmer-dsl-libui-windows-basic-window.png](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-libui/master/images/glimmer-dsl-libui-windows-basic-window.png)

Linux Screenshot:

![glimmer-dsl-libui-linux-basic-window.png](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-libui/master/images/glimmer-dsl-libui-linux-basic-window.png)




## Basic Table Progress Bar


This is an example that demonstrates the `table` control in [Glimmer DSL for LibUI](https://rubygems.org/gems/glimmer-dsl-libui).

The `table` `cell_rows` property declaration results in "implicit data-binding" between the `table` control and `Array` of `Arrays`; a new [Glimmer DSL for LibUI](https://rubygems.org/gems/glimmer-dsl-libui) innovation that provides convenience automatic support for:
- Deleting cell rows: Calling `Array#delete`, `Array#delete_at`, `Array#delete_if`, or any filtering/deletion `Array` method automatically deletes rows in actual `table` control
- Inserting cell rows: Calling `Array#<<`, `Array#push`, `Array#prepend`, or any insertion/addition `Array` method automatically inserts rows in actual `table` control
- Changing cell rows: Calling `Array#[]=`, `Array#map!`, or any update `Array` method automatically updates rows in actual `table` control


```ruby
require 'glimmer-dsl-libui'

include Glimmer

data = [
  ['task 1', 0],
  ['task 2', 15],
  ['task 3', 100],
  ['task 4', 75],
  ['task 5', -1],
]

window('Task Progress', 300, 200) {
  vertical_box {
    table {
      text_column('Task')
      progress_bar_column('Progress')

      cell_rows data # implicit data-binding
    }
    
    button('Mark All As Done') {
      stretchy false
      
      on_clicked do
        data.each_with_index do |row_data, row|
          data[row][1] = 100 # automatically updates table due to implicit data-binding
        end
      end
    }
  }
}.show
```


Mac

![glimmer-dsl-libui-mac-basic-table-progress-bar.png](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-libui/master/images/glimmer-dsl-libui-mac-basic-table-progress-bar.png)

Windows

![glimmer-dsl-libui-windows-basic-table-progress-bar.png](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-libui/master/images/glimmer-dsl-libui-windows-basic-table-progress-bar.png)

Linux

![glimmer-dsl-libui-linux-basic-table-progress-bar.png](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-libui/master/images/glimmer-dsl-libui-linux-basic-table-progress-bar.png)



## Snake


Snake provides an example of building a desktop application [test-first](https://github.com/AndyObtiva/glimmer-dsl-libui/blob/master/spec/examples/snake/model/game_spec.rb) following the MVP ([Model](https://github.com/AndyObtiva/glimmer-dsl-libui/blob/master/examples/snake/model/game.rb) / [View](https://github.com/AndyObtiva/glimmer-dsl-libui/blob/master/examples/snake.rb) / [Presenter](https://github.com/AndyObtiva/glimmer-dsl-libui/blob/master/examples/snake/presenter/grid.rb)) architectural pattern.

[examples/snake.rb](https://github.com/AndyObtiva/glimmer-dsl-libui/blob/master/examples/snake.rb)


```ruby
require 'glimmer-dsl-libui'
require 'glimmer/data_binding/observer'

require_relative 'snake/presenter/grid'

class Snake
  CELL_SIZE = 15
  SNAKE_MOVE_DELAY = 0.1
  include Glimmer
  
  def initialize
    @game = Model::Game.new
    @grid = Presenter::Grid.new(@game)
    @game.start
    create_gui
    register_observers
  end
  
  def launch
    @main_window.show
  end
  
  def register_observers
    @game.height.times do |row|
      @game.width.times do |column|
        Glimmer::DataBinding::Observer.proc do |new_color|
          @cell_grid[row][column].fill = new_color
        end.observe(@grid.cells[row][column], :color)
      end
    end
    
    Glimmer::DataBinding::Observer.proc do |game_over|
      Glimmer::LibUI.queue_main do
        if game_over
          msg_box('Game Over!', "Score: #{@game.score}")
          @game.start
        end
      end
    end.observe(@game, :over)
    
    Glimmer::LibUI.timer(SNAKE_MOVE_DELAY) do
      unless @game.over?
        @game.snake.move
        @main_window.title = "Glimmer Snake (Score: #{@game.score})"
      end
    end
  end
  
  def create_gui
    @cell_grid = []
    @main_window = window('Glimmer Snake', @game.width * CELL_SIZE, @game.height * CELL_SIZE) {
      resizable false
      
      vertical_box {
        padded false
        
        @game.height.times do |row|
          @cell_grid << []
          horizontal_box {
            padded false
            
            @game.width.times do |column|
              area {
                @cell_grid.last << path {
                  square(0, 0, CELL_SIZE)
                  
                  fill Presenter::Cell::COLOR_CLEAR
                }
                
                on_key_up do |area_key_event|
                  orientation_and_key = [@game.snake.head.orientation, area_key_event[:ext_key]]
                  case orientation_and_key
                  in [:north, :right] | [:east, :down] | [:south, :left] | [:west, :up]
                    @game.snake.turn_right
                  in [:north, :left] | [:west, :down] | [:south, :right] | [:east, :up]
                    @game.snake.turn_left
                  else
                    # No Op
                  end
                end
              }
            end
          }
        end
      }
    }
  end
end

Snake.new.launch
```


Mac

![glimmer-dsl-libui-mac-snake.png](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-libui/master/images/glimmer-dsl-libui-mac-snake.png)

![glimmer-dsl-libui-mac-snake-game-over.png](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-libui/master/images/glimmer-dsl-libui-mac-snake-game-over.png)

Windows

![glimmer-dsl-libui-windows-snake.png](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-libui/master/images/glimmer-dsl-libui-windows-snake.png)

![glimmer-dsl-libui-windows-snake-game-over.png](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-libui/master/images/glimmer-dsl-libui-windows-snake-game-over.png)

Linux

![glimmer-dsl-libui-linux-snake.png](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-libui/master/images/glimmer-dsl-libui-linux-snake.png)

![glimmer-dsl-libui-linux-snake-game-over.png](https://raw.githubusercontent.com/AndyObtiva/glimmer-dsl-libui/master/images/glimmer-dsl-libui-linux-snake-game-over.png)



## Find Out More

### References

- Home :: [github.com/AndyObtiva/glimmer-dsl-libui](https://github.com/AndyObtiva/glimmer-dsl-libui)
- Gem :: [glimmer-dsl-libui](https://rubygems.org/gems/glimmer-dsl-libui)
- Docs :: [glimmer-dsl-libui](https://rubydoc.info/gems/glimmer-dsl-libui)
- Blog :: [Snake Game and Other Glimmer DSL for LibUI Articles](http://andymaleh.blogspot.com/search/label/LibUI)
- Video :: [Whatever Happened to Desktop Development in Ruby?, MountainWest RubyConf 2011](https://confreaks.tv/videos/mwrc2011-whatever-happened-to-desktop-development-in-ruby)
