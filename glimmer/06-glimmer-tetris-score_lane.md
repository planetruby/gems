# Day 6 - glimmer-dsl-swt Gem - Glimmer Tetris - The Score Lane - See Your Game Progress!

Written by {% avatar AndyObtiva %} [Andy Maleh](https://github.com/AndyObtiva)

_Software Engineering Expert from Montreal, Quebec. Creator of [Glimmer](https://github.com/AndyObtiva/glimmer) and [Abstract Feature Branch](https://github.com/AndyObtiva/abstract_feature_branch). Speaker at RailsConf, RubyConf, AgileConf, EclipseCon, EclipseWorld. Master in Software Engineering, DePaul University, Chicago. Blogs at [Code Mastery Takes Commitment To Bold Coding Adventures](http://andymaleh.blogspot.com/). Snowboarder and Drummer._

## Goal

Show The Score Lane indicating the next Tetromino shape to drop (aka preview next Tetromino), the current score, the number of lines eliminated, and the current level.

## Refactorings

What we are building in this article is what we call The Score Lane. But, before we add it, let us do some quick and fun refactorings!

First of all, although `AppView`'s `body {}` is pretty compact, would you believe me if I tell you we can do better?!!

Let us take advantage of Glimmer Custom Widgets to demonstrate.

Run this command in the terminal (ZShell friendly version):

```
glimmer "scaffold:customwidget[playfield]"
```

This should generate an `app/glimmer_tetris/view/playfield.rb` file.

Now, delete the content of `body {}` in `Playfield` (and delete all comments in the file).

Cut the following code from `AppView`:

```ruby
          composite((:double_buffered unless OS.mac?)) {
            grid_layout {
              num_columns Model::Game::PLAYFIELD_WIDTH
              make_columns_equal_width true
              margin_width BLOCK_SIZE
              margin_height BLOCK_SIZE
              horizontal_spacing 0
              vertical_spacing 0
            }
  
            Model::Game::PLAYFIELD_HEIGHT.times do |row|
              Model::Game::PLAYFIELD_WIDTH.times do |column|
                canvas { |canvas_proxy|
                  layout_data {
                    width_hint BLOCK_SIZE
                    height_hint BLOCK_SIZE
                  }
                  
                  bevel(size: BLOCK_SIZE) {
                    base_color bind(@game.playfield[row][column], :color)
                  }
                }
              end
            end
          }
```

And, paste it inside the `body {}` block in `Playfield`.

Finally, go back to `AppView` and add the following require statement right above the class declaration:

```ruby
require_relative 'playfield'
```

Afterwards, go down to where you cut the code at the bottom of the `shell {}` block and add this single keyword:

```ruby
            playfield
```

This is how `AppView`'s `body {}` should be now:

```ruby
      body {
        shell(:no_resize) {
          grid_layout
          text 'Glimmer Tetris'

          playfield
        }
      }
```

This is how the `Playfield` entire content should be now:

```ruby
class GlimmerTetris
  module View
    class Playfield
      include Glimmer::UI::CustomWidget
  
      body {
        composite((:double_buffered unless OS.mac?)) {
          grid_layout {
            num_columns Model::Game::PLAYFIELD_WIDTH
            make_columns_equal_width true
            margin_width BLOCK_SIZE
            margin_height BLOCK_SIZE
            horizontal_spacing 0
            vertical_spacing 0
          }

          Model::Game::PLAYFIELD_HEIGHT.times do |row|
            Model::Game::PLAYFIELD_WIDTH.times do |column|
              canvas { |canvas_proxy|
                layout_data {
                  width_hint BLOCK_SIZE
                  height_hint BLOCK_SIZE
                }
                
                bevel(size: BLOCK_SIZE) {
                  base_color bind(@game.playfield[row][column], :color)
                }
              }
            end
          end
        }
      }
  
    end
  end
end
```

Finally, to confirm that the refactoring worked, let us run:

```
glimmer run
```

You should get an error that includes this statement:

```
...undefined method `playfield' for nil:NilClass (NoMethodError)...
```

Good! That means, we need to declare a Custom Widget option.

Just below the `include Glimmer::UI::CustomWidget` statement in `Playfield`, add the following:

```ruby
      option :game_playfield
```

Now, go to this line inside the `body {}` block:

```ruby
                base_color bind(@game.playfield[row][column], :color)
```

And, change to:

```ruby
                base_color bind(game_playfield[row][column], :color)
```

Finally, go to `AppView` and update `playfield` with:

```ruby
          playfield(game_playfield: @game.playfield)
```

Run:

```
glimmer run
```

This time, it should work fine!

(note: if you ever get stuck with bad code, remember you can checkout working Code inside a repo in the References below)

Can you believe we have been able to reduce the potentially already smallest Tetris implementation on Earth to this for the GUI part?

```ruby
      body {
        shell(:no_resize) {
          grid_layout
          text 'Glimmer Tetris'

          playfield(game_playfield: @game.playfield)
        }
      }
```

Something is not quite right though. After all, we only moved the code representing The Playfield to `Playfield`, but
we did not improve anything in its own code. Can we go more granular? Yes! The `canvas`! It is simply representing the
concept of a `block`

Run this command in the terminal (shorter version of previous custom widget scaffolding command):

```
glimmer "scaffold:cw[block]"
```

This should generate `app/glimmer_tetris/view/block`.

Go back to `Playfield` and cut this code:

```ruby
                canvas { |canvas_proxy|
                  layout_data {
                    width_hint BLOCK_SIZE
                    height_hint BLOCK_SIZE
                  }
                  
                  bevel(size: BLOCK_SIZE) {
                    base_color bind(@game.playfield[row][column], :color)
                  }
                }
```

Type this in its place:

```ruby
                block
```

Also, add this above the declaration of the top class:

```ruby
require_relative 'block'
```

Now, open `Block`, delete comments, and clear the `body {}` block.

Finally, paste the code you cut from `Playfield` inside the `body {}` block.

Run:

```
glimmer run
```

You should get an error including this message:
```
...Glimmer keyword game_playfield with args [] cannot be handled inside parent...
```

Again, that is good! It means we need to add Custom Widget options.

Just below the `include Glimmer::UI::CustomWidget` declaration, add the following code:

```ruby
  
  option :game_block
```

Replace this line in `Block`:

```ruby
            base_color bind(game_playfield[row][column], :color)
```

with this line:

```ruby
            base_color bind(game_block, :color)
```

Finally, go back to `Playfield` and replace this line:

```ruby
                block
```

with this line:

```ruby
                block(game_block: game_playfield[row][column])
```

Run:

```
glimmer run
```

The game should be working again!

One last note is usually it is better to keep layout data out of an extracted Custom Widget to keep it flexible in accomodating
different layouts with different layout data.

Let's do one last refactoring regarding the `layout_data` inside `Block`.

Cut this line from `Block`:

```ruby
          layout_data {
            width_hint BLOCK_SIZE
            height_hint BLOCK_SIZE
          }
```

Go to `Playfield`, and replace this line:

```ruby
                block(game_block: game_playfield[row][column])
```

with the following:

```ruby
                block(game_block: game_playfield[row][column]) {
                  layout_data {
                    width_hint BLOCK_SIZE
                    height_hint BLOCK_SIZE
                  }
                }
```

See, Glimmer DSL for SWT lets you treat `block` as a first-class-citizen widget, which can nest its own `layout_data` just like `label` or `combo`

Now, run:

```
glimmer run
```

The game should remain happily functional!

## Score Lane

Now, we are ready to add The Score Lane. I am sure you can guess how. By using Custom Widgets and Scaffolding!

I will leave this section for you to do as an exercise with the following tips:
- It will reuse the `playfield` Custom Widget but with a different playfield `game.preview_playfield` and different dimensions: `Model::Game::PREVIEW_PLAYFIELD_WIDTH` & `Model::Game::PREVIEW_PLAYFIELD_HEIGHT` (note that this means you will have to refactor `Playfield` first to support passing `playfield_width` and `playfield_height` options)
- It will rely on labels, a single-column vertical layout, and data-binding to `game.score`, `game.lines`, and `game.level`
- It will require the `shell` layout to have 2 columns

This is how The Score Lane must look like:

![Glimmer Tetris](https://github.com/AMaleh/glimmer-dsl-swt/raw/master/images/glimmer-tetris.png)

To know if you did well, know that at the end you should have code like this `ScoreLane` class under `app/glimmer_tetris/view/score_lane.rb`:

```ruby
require_relative 'playfield'

class GlimmerTetris
  module View
    class ScoreLane
      include Glimmer::UI::CustomWidget
  
      options :game
      
      body {
        composite {
          row_layout {
            type :vertical
            center true
            fill true
            margin_width 0
            margin_right BLOCK_SIZE
            margin_height BLOCK_SIZE
          }
          label(:center) {
            text 'Next'
            font name: FONT_NAME, height: FONT_TITLE_HEIGHT, style: FONT_TITLE_STYLE
          }
          playfield(game_playfield: game.preview_playfield, playfield_width: Model::Game::PREVIEW_PLAYFIELD_WIDTH, playfield_height: Model::Game::PREVIEW_PLAYFIELD_HEIGHT)

          label(:center) {
            text 'Score'
            font name: FONT_NAME, height: FONT_TITLE_HEIGHT, style: FONT_TITLE_STYLE
          }
          label(:center) {
            text bind(game, :score)
            font height: FONT_TITLE_HEIGHT
          }
          
          label # spacer
          
          label(:center) {
            text 'Lines'
            font name: FONT_NAME, height: FONT_TITLE_HEIGHT, style: FONT_TITLE_STYLE
          }
          label(:center) {
            text bind(game, :lines)
            font height: FONT_TITLE_HEIGHT
          }
          
          label # spacer
          
          label(:center) {
            text 'Level'
            font name: FONT_NAME, height: FONT_TITLE_HEIGHT, style: FONT_TITLE_STYLE
          }
          label(:center) {
            text bind(game, :level)
            font height: FONT_TITLE_HEIGHT
          }
        }
      }
    end
  end
end
```

`Playfield` should have code like this:

```ruby
require_relative 'block'

class GlimmerTetris
  module View
    class Playfield
      include Glimmer::UI::CustomWidget
  
      options :game_playfield, :playfield_width, :playfield_height
  
      body {
        composite((:double_buffered unless OS.mac?)) {
          grid_layout {
            num_columns playfield_width
            make_columns_equal_width true
            margin_width BLOCK_SIZE
            margin_height BLOCK_SIZE
            horizontal_spacing 0
            vertical_spacing 0
          }
          
          playfield_height.times do |row|
            playfield_width.times do |column|
              block(game_block: game_playfield[row][column]) {
                layout_data {
                  width_hint BLOCK_SIZE
                  height_hint BLOCK_SIZE
                }
              }
            end
          end
        }
      }
  
    end
  end
end
```

Finally, `AppView` should have code like this:

```ruby
require_relative '../model/game'

require_relative 'playfield'
require_relative 'score_lane'

class GlimmerTetris
  module View
    class AppView
      include Glimmer::UI::CustomShell

      attr_reader :game
      
      before_body {
        @mutex = Mutex.new
        @game = Model::Game.new
            
        @game.configure_beeper do
          display.beep
        end
        
        Display.app_name = 'Glimmer Tetris'
    
        display {
          on_swt_keydown { |key_event|
            case key_event.keyCode
            when swt(:arrow_down), 's'.bytes.first
              game.down! if OS.mac?
            when swt(:arrow_up)
              case game.up_arrow_action
              when :instant_down
                game.down!(instant: true)
              when :rotate_right
                game.rotate!(:right)
              when :rotate_left
                game.rotate!(:left)
              end
            when swt(:arrow_left), 'a'.bytes.first
              game.left!
            when swt(:arrow_right), 'd'.bytes.first
              game.right!
            when swt(:shift), swt(:alt)
              if key_event.keyLocation == swt(:right) # right shift key
                game.rotate!(:right)
              elsif key_event.keyLocation == swt(:left) # left shift key
                game.rotate!(:left)
              end
            end
          }
    
          # if running in app mode, set the Mac app about dialog (ignored in platforms)
          on_about {
            show_about_dialog
          }
          
          on_quit {
            exit(0)
          }
        }
      }
      
      after_body {
        observe(@game, :game_over) do |game_over|
          if game_over
            show_game_over_message_box
          else
            start_moving_tetrominos_down
          end
        end
        @game.start!
      }
      
      body {
        shell(:no_resize) {
          grid_layout 2, false
          text 'Glimmer Tetris'
          minimum_size 475, 500

          playfield(game_playfield: @game.playfield, playfield_width: Model::Game::PLAYFIELD_WIDTH, playfield_height: Model::Game::PLAYFIELD_HEIGHT)
          
          score_lane(game: @game)
        }
      }
      
      def start_moving_tetrominos_down
        Thread.new do
          @mutex.synchronize do
            loop do
              time = Time.now
              sleep @game.delay
              break if @game.game_over? || body_root.disposed?
              # ensure entire game tetromino down movement happens as one GUI update event with sync_exec (to avoid flicker/stutter)
              sync_exec {
                @game.down! unless @game.paused?
              }
            end
          end
        end
      end
      
      def show_game_over_message_box
        message_box {
          text 'Game Over'
          message 'Play Again?'
        }.open # this blocks until closed
        @game.start!
      end
      
      def show_about_dialog
        message_box {
          text 'Glimmer Tetris'
          message "Glimmer Tetris\n\nGlimmer DSL for SWT Sample\n\nCopyright (c) 2021 Andy Maleh"
        }.open
      end
    end
  end
end
```

Notice the ultra-compact `body {}`:

```ruby
      body {
        shell(:no_resize) {
          grid_layout 2, false
          text 'Glimmer Tetris'

          playfield(game_playfield: @game.playfield, playfield_width: Model::Game::PLAYFIELD_WIDTH, playfield_height: Model::Game::PLAYFIELD_HEIGHT)
          
          score_lane(game: @game)
        }
      }
```

Unheard of anywhere else, eh!?

Stay tuned for the [next article](07-glimmer-tetris-high-score-dialog-menu-bar-icon.md) where we cover High Scores!

## Find Out More

### References

- Code :: [Glimmer Tetris](https://github.com/AMaleh/glimmer-tetris)
- Gem  :: [Glimmer DSL for SWT](https://github.com/AMaleh/glimmer-dsl-swt)
- Blog :: [Glimmer Tetris in One Day! and Many More Glimmer Articles](http://andymaleh.blogspot.com/search/label/Glimmer)
