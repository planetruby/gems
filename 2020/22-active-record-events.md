# Day 22 - active_record-events Gem - Complete? Record Your Own Timestamps in Active Record Models with has_event Macro, Automagically

Written by {% avatar pienkowb %} [Bartosz Pieńkowski](https://github.com/pienkowb)



## Timestamping in Active Record models

Recording a timestamp in order to mark that an event occurred to an object is a common practice when dealing with Active Record models.
Active Record itself gives us a good example of such an approach.
It automatically stores the time when a record was created or updated in the `created_at` and `updated_at` fields.

Similarly, we can create custom timestamp fields by adding `datetime` columns in the database.
Yet in order to conveniently manage such fields, we would need to accompany each of them with a bunch of methods in the model class.
The more timestamp fields we want to manage, the more methods we need.
As a result, the model gets bigger and bigger.

That's where the `active_record-events` gem comes into play.

## Using the `active_record-events` gem

The gem provides us with the `has_event` macro which adds convenience methods on top of a `datetime` field.

Let's look at the following example.
Assume we have a `Task` model with a `completed_at` field.
Now, let's add the `has_event` macro inside the model class:

```ruby
class Task < ActiveRecord::Base
  has_event :complete
end
```

As a result, we get plenty of methods for managing the field without the need to define them explicitly.

```ruby
task = Task.create!

task.completed? # => false

task.complete

task.completed? # => true
task.completed_at # => Sun, 20 Dec 2020 16:54:11 UTC +00:00
```

The generated methods allow us to check if a timestamp was recorded (`task.completed?`, `task.not_completed?`), record or overwrite a timestamp (`task.complete`, `task.complete!`), as well as record multiple timestamps at once (`Task.complete_all`).

Additionally, the macro defines two scope methods for retrieving objects with and without a recorded timestamp (`Task.completed`, `Task.not_completed`).

All of this can be achieved at the cost of a simple one-liner.

## Generating a migration

Before we can record a timestamp, we need to add the `completed_at` column to the `tasks` table in our database.
In order to do that, we could manually create a migration file, but it's much easier to use a generator provided by the gem:

```
$ rails generate active_record:event task complete
```

This will create a necessary migration and insert a `has_event` statement into the corresponding model class.

```ruby
# db/migrate/XXX_add_completed_at_to_tasks.rb

class AddCompletedAtToTasks < ActiveRecord::Migration[6.0]
  def change
    add_column :tasks, :completed_at, :datetime
  end
end
```

```ruby
# app/models/task.rb

class Task < ActiveRecord::Base
  has_event :complete
end
```

## Find out more

### Alternatives

In more complex scenarios, you might consider using a state machine gem (e.g. [`aasm`](https://github.com/aasm/aasm), [`workflow`](https://github.com/geekq/workflow) or [`statesman`](https://github.com/gocardless/statesman)).
Another alternative is [ActiveRecord::Enum](https://api.rubyonrails.org/classes/ActiveRecord/Enum.html), which offers similar functionality with a different underlying mechanism.

### References

- Home :: [github.com/pienkowb/active_record-events](https://github.com/pienkowb/active_record-events)
- Gem :: [active_record-events](https://rubygems.org/gems/active_record-events)
- Screencast :: [ActiveRecord::Events - Awesome Ruby Gems](https://www.youtube.com/watch?v=TIR7YDF3O-4)
