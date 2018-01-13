# Week 9 - state_machine(s) gem - model processes and work flows with finite state machines (FSM) and automata theory


## What's a state machine? What's a finite state machine (FSM)?

A state machine or finite state machine (FSM)
lets you model a traffic light, a shopping cart checkout procedure,
a conference proposal process, a credit card transaction and many more "real world"
tasks using 1) states, 2) events, and 3) transitions.
Or to quote the Wikipedia:

> A finite-state machine (FSM) or finite-state automaton (plural: automata),
> or simply a state machine, is a mathematical model of computation used to design both computer programs
> and sequential logic circuits.
> It is conceived as an abstract machine that can be in one of a finite number of states.
> The machine is in only one state at a time; the state it is in at any given time is called the current state.
> It can change from one state to another when initiated by a triggering event or condition;
> this is called a transition. A particular FSM is defined by a list of its states,
> and the triggering condition for each transition.


## Example: The State of the Football Match

![](http://planetruby.github.io/_posts/i/state_machine_match.png)

Let's start defining a state machine for modelling football matches in Ruby.

~~~
class Match

  state_machine :initial => :scheduled do
    event :start do
      transition :scheduled => :playing
    end
    event :finish do
      transition :playing => :finished
    end
  end

end
~~~

That you can use like:

~~~
m = Match.new
m.state        # => :scheduled
m.scheduled?   # => true
m.playing?     # => false
m.finished?    # => false
m.can_finish?  # => false
m.can_start?   # => true

m.start        # transition from :scheduled to :playing by calling event method e.g. start
m.state        # => :playing
m.playing?     # => true
m.can_start?   # => false
m.can_finish?  # => true

m.finish
m.state        # => Trivia Quiz: a) :scheduled, b) :playing, c) :finished
~~~

The `state` is a variable that holds - surprise, surprise - the state of the model.
All possible states are strings (symbols) e.g. `:scheduled`, `:playing`, `:finished`.

Events are methods (e.g. `Match#start`, `Match#finish`) that change the state of the model
(that is, transition from one state to another e.g. from `:playing` to `:finished`).


Why use a state machine?  Because it lets you do more with less code thanks to
automata theory packed-up into ready-to-use gems for use in your models in practice.


## What's the state_machine(s) gem?

Let's thank Aaron Pfeifer and friends - who started the state_machine gem and Abdelkader Boudih
and friends - who continue with the state_machines gem
that lets you create state machines for attributes on any class in Ruby.

Let's continue the football match example - yes, it's the state_machine gem in action - and let's
add more states. Football matches in the real-world can get canceled, postponed, awarded points
if canceled or can get rescheduled or matches can get scheduled for a matchday but not yet timed
for a fixed day and time and so on and on. A picture says more than a thousand words:

![](http://planetruby.github.io/_posts/i/state_machine_match_ii.png)

Let's define the state machine in Ruby using the state_machine mini language:

~~~
class Match

  state_machine :initial => :scheduled do
    event :time do
      transition [:scheduled,:postponed] => :timed
    end
    event :postpone do
      transition :timed => :postponed
    end
    event :start do
      transition :timed => :playing
    end
    event :cancel do
      transition [:timed,:postponed,:playing] => :canceled
    end
    event :reschedule do
      transition :canceled => :scheduled
    end
    event :finish do
      transition :playing => :finished
    end
    event :award do
      transition :canceled => :awarded
    end
  end

end
~~~

And let's try it out:

~~~
m = Match.new
m.state        # => :scheduled
m.scheduled?   # => true
m.timed?       # => false
m.can_start?   # => false
m.time
m.timed?       # => true
m.can_start?   # => true
m.start
m.playing?     # => true
m.finished?    # => false
m.can_cancel?  # => true
...
~~~

What else can state_machine do for you? Let's store the state in a database table with ActiveRecord.
First let's create a table with the required column - surprise, surprise - called `state`:

~~~
create_table :matches do |t|
  t.string :state
end
~~~

And derive the Match class from `ActiveRecord::Base`:

~~~
class Match < ActiveRecord::Base
  ## same as above
end
~~~

That's it. Let's test drive the script with an in-memory SQLite database:

~~~
require 'active_record'
require 'state_machine'

ActiveRecord::Base.establish_connection(
  adapter:  'sqlite3',
  database: ':memory:'
)

ActiveRecord::Schema.define do
  create_table :matches do |t|
    t.string :state
  end
end


class Match < ActiveRecord::Base
  ## same as above
end

m = Match.create!   # creates the record in the database table using SQL
                    #  e.g INSERT INTO matches (state) VALUES ("scheduled")
m.state             # => :scheduled
m.time              # gets auto-saved (and auto-commited)
                    #  e.g. UPDATE matches SET state = "timed" WHERE matches.id = 1
m.timed?            # => true
m.finished?         # => false
m.start             # again gets auto-saved (and auto-commited)
                    #  e.g. UPDATE matches SET state = "playing" WHERE matches.id = 1
m.state             # => Trivia Quiz: a) :scheduled, b) :playing, c) :finished
...
~~~

That's just the beginning. More state_machine highlights include:

- Validations
- Hooks e.g. before/after/around/failure transition callbacks
- State predicates
- Attribute-based event transitions
- Multiple state machines on a single class and namespaced state machines
- State values of any data type (use integer instead of strings/symbols etc.)
- GraphViz visualization creator
- And much more


## Bonus - Order Processing Example by Markus Prinz

Thanks to Markus Prinz - who talked about "Ghost in the State Machine"
at the last Vienna.rb meetup and lets us share the order state machine example:

~~~
require 'state_machine'

class Order
  state_machine :state, :initial => :new do
    around_transition do |order, transition, block|
      puts "Starting transition from #{transition.from}"
      block.call
      puts "Finished transition to #{transition.to}"
    end

    event :submit do
      transition :new => :payment_waiting
    end

    event :payment_received do
      transition :payment_waiting => :waiting_for_processing
    end

    event :payment_failed do
      transition :payment_waiting => :payment_failed
    end

    event :retry_payment do
      transition :payment_failed => :payment_waiting
    end

    event :process do
      transition :waiting_for_processing => :waiting_for_shipping
    end

    event :ship do
      transition :waiting_for_shipping => :shipped
    end
    after_transition :on => :ship, :do => :notify_customer

    # Multiple transitions, first matching is taken
    event :cancel do
      transition :waiting_for_processing => :canceled
      transition :waiting_for_shipping => :canceled
      transition :shipped => :waiting_for_return
    end

    event :return_shipment_received do
      transition :waiting_for_return => :canceled
    end
  end

  def notify_customer
    puts "Your package has been shipped! :shipit:"
  end
end

if __FILE__ == $0
  order_fsm = Order.new
  order_fsm.submit!
  order_fsm.payment_received!
  order_fsm.process!
  order_fsm.ship!

  puts "\n\n"

  print "Received payment twice (soft): "
  order_fsm.payment_received
  puts order_fsm.state
  puts "Received payment twice:"
  order_fsm.payment_received!
end
~~~

(Source: [Ghost in the State Machine](https://speakerdeck.com/cypher/ghost-in-the-state-machine), Talk Slides)


## Find Out More

state_machines (ActiveRecord 4.1+)

* home     :: [github.com/state-machines/state_machines](https://github.com/state-machines/state_machines)
* gem      :: [rubygems.org/gems/state_machines](https://rubygems.org/gems/state_machines)
* rdoc     :: [rubydoc.info/gems/state_machines](http://rubydoc.info/gems/state_machines)

state_machine (ActiveRecord up-to 4.0.x)

* home     :: [github.com/pluginaweek/state_machine](https://github.com/pluginaweek/state_machine)
* gem      :: [rubygems.org/gems/state_machine](https://rubygems.org/gems/state_machine)
* rdoc     :: [rubydoc.info/gems/state_machine](http://rubydoc.info/gems/state_machine)
