# Week 7 - logutils gem - yet another (lightweight, simple) logging library in Ruby



Sooner or later you will debug your code and add some print statements to help you along. Example:

~~~
puts "[debug] value: #{value}"
puts "[debug] value.class.name: #{value.class.name}
~~~

A "better" way is to use a logger that lets you turn off and on your
debug messages as needed and why not a add some more message classes for
errors or warnings, for example?

The "classic" message class hierarchy reads:

- [OFF]
- FATAL
- ERROR
- WARN
- INFO
- DEBUG
- [ALL]


## What's the logutils gem?

Using the logutils gem - yet another logging library - you can
print all messages and more. Start by getting a logger e.g.:

~~~
logger = LogUtils::Logger.new
~~~

And now you can use the "standard" methods such as `#debug`, `#info`, `#warn`, etc.

~~~
logger.debug "msg"
logger.info "another msg"
logger.warn "another msg"
logger.error "another msg"
logger.fatal "another msg"
~~~

To turn on/off debug messages or any others in the hierarchy use the level attribute.
Example:

~~~
logger.level = :info
~~~

Will print only message of class info and above and, thus, turn off debug messages.


### Logging Mixin

What else?  For your convenience you can include the logging machinery in a Ruby class
with a single line using the Logging mixin e.g.

~~~
include LogUtils::Logging
~~~

This will add/mixin the logger attribute reader e.g.

~~~
def logger
  @logger ||= Logger[ self ]
end
~~~

And you're all setup to start logging. Example:

~~~
class SampleClass

 include Logging

  def initialize
    logger.info 'Hello, SampleClass!'
  end
end
~~~


### Bonus: Log to the database using `LogDb`

To log to the database use an addon, that is, the logutils-activerecord gem.
Example:

~~~
require 'logutils'
require 'logutils/activerecord'   # Note: will also require 'active_record'

include LogUtils    # lets you use Logger instead of LogUtils::Logger

logger = Logger[ 'Test' ]
logger.info 'Hello, LogUtils!'

LOG_DB_CONFIG = {
  adapter:   'sqlite3',
  database:  ':memory:'
}

ActiveRecord::Base.establish_connection( LOG_DB_CONFIG )

LogDb.create   # create logs table
LogDb.setup    # setup handler that saves logs in the database

logger.info '¡Hola! LogUtils'
logger.warn 'Servus LogUtils!'
~~~


To create the logs table in the database use:

~~~
LogDb.create
~~~

Just a shortcut for:

~~~
create_table :logs do |t|
  t.string  :msg,   null: false
  t.string  :level, null: false  # e.g. fatal, error, warn, info, debug
  t.string  :app
  t.string  :tag
  t.integer :pid
  t.integer :tid, limit: 8
  t.string  :ts
  t.timestamps
end
~~~

To start logging to the database  add a log event handler that stores
log messages in the database. Use:

~~~
LogDb.setup
~~~

To dump all log messages to the console you can use the log model. Example:

~~~
LogDb::Model::Log.order('created_at DESC').each do |log|
  puts "[#{log.level}] #{log.msg}"
end
~~~

Will print:

~~~
[info] ¡Hola! LogUtils
[warn] Servus LogUtils!
~~~

That's it. Happy logging.


## Find Out More

logutils

* home     :: [github.com/rubylibs/logutils](https://github.com/rubylibs/logutils)
* gem      :: [rubygems.org/gems/logutils](https://rubygems.org/gems/logutils)
* rdoc     :: [rubydoc.info/gems/logutils](http://rubydoc.info/gems/logutils)


logutils-activerecord

* home     :: [github.com/rubylibs/logutils-activerecord](https://github.com/rubylibs/logutils-activerecord)
* gem      :: [rubygems.org/gems/logutils-activerecord](https://rubygems.org/gems/logutils-activerecord)
* rdoc     :: [rubydoc.info/gems/logutils-activerecord](http://rubydoc.info/gems/logutils-activerecord)
