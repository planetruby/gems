# Week 10 - tilt gem - let's build (yet another) micro web framework in less than 33 lines of code


Do you think the Ruby on Rails web framework is a massive monster - more than 10,000+ lines of code
just for `ActionPack` - not counting `ActiveModel`, `ActiveRecord`, `ActiveJob`, `ActiveResource`
and what not?
Or do you think that even the "classic" Sinatra web machinery clocking
in at more than 1,000+ lines of code
is more macro than micro?

Why not write a "real" micro web framework inspired by
Konstantin Haase's Almost Sinatra hack that ships as a 6(!) lines of code pastie:

`almost_sinatra.rb`:

~~~
%w.rack tilt date INT TERM..map{|l|trap(l){$r.stop}rescue require l};$u=Date;$z=($u.new.year + 145).abs;puts "== Almost Sinatra/No Version has taken the stage on #$z for development with backup from Webrick"
$n=Module.new{extend Rack;a,D,S,q=Rack::Builder.new,Object.method(:define_method),/@@ *([^\n]+)\n(((?!@@)[^\n]*\n)*)/m
%w[get post put delete].map{|m|D.(m){|u,&b|a.map(u){run->(e){[200,{"Content-Type"=>"text/html"},[a.instance_eval(&b)]]}}}}
Tilt.mappings.map{|k,v|D.(k){|n,*o|$t||=(h=$u._jisx0301("hash, please");File.read(caller[0][/^[^:]+/]).scan(S){|a,b|h[a]=b};h);v[0].new(*o){n=="#{n}"?n:$t[n.to_s]}.render(a,o[0].try(:[],:locals)||{})}}
%w[set enable disable configure helpers use register].map{|m|D.(m){|*_,&b|b.try :[]}};END{Rack::Handler.get("webrick").run(a,Port:$z){|s|$r=s}}
%w[params session].map{|m|D.(m){q.send m}};a.use Rack::Session::Cookie;a.use Rack::Lock;D.(:before){|&b|a.use Rack::Config,&b};before{|e|q=Rack::Request.new e;q.params.dup.map{|k,v|params[k.to_sym]=v}}}
~~~

Of course - the "magic" is possible because all the machinery and heavy-lifting
gets outsourced to `rack` and to the lesser known `tilt` library.


## What's the tilt gem?

Let's thank Ryan Tomayko, Magnus Holm and friends
for creating the tilt gem offering a standard "generic" interface
for template engines that you can use - surprise, surprise - for building your own
web frameworks or static site generators, for example.


Let's check-up what formats and template engines tilt includes out-of-the-box:

~~~
require 'tilt'

Tilt.mappings.each do |ext, engines|
  puts "#{ext.ljust(12)} : #{engines.inspect}"
end
~~~

Will result in:

~~~
str          : [Tilt::StringTemplate]
erb          : [Tilt::ErubisTemplate, Tilt::ERBTemplate]
rhtml        : [Tilt::ErubisTemplate, Tilt::ERBTemplate]
erubis       : [Tilt::ErubisTemplate]
etn          : [Tilt::EtanniTemplate]
etanni       : [Tilt::EtanniTemplate]
haml         : [Tilt::HamlTemplate]
sass         : [Tilt::SassTemplate]
scss         : [Tilt::ScssTemplate]
less         : [Tilt::LessTemplate]
rcsv         : [Tilt::CSVTemplate]
coffee       : [Tilt::CoffeeScriptTemplate]
nokogiri     : [Tilt::NokogiriTemplate]
builder      : [Tilt::BuilderTemplate]
mab          : [Tilt::MarkabyTemplate]
liquid       : [Tilt::LiquidTemplate]
radius       : [Tilt::RadiusTemplate]
markdown     : [Tilt::RedcarpetTemplate, Tilt::RedcarpetTemplate::Redcarpet2, Tilt::RedcarpetTemplate::Redcarpet1, Tilt::RDiscountTemplate, Tilt::BlueClothTemplate, Tilt::KramdownTemplate, Tilt::MarukuTemplate]
mkd          : [Tilt::RedcarpetTemplate, Tilt::RedcarpetTemplate::Redcarpet2, Tilt::RedcarpetTemplate::Redcarpet1, Tilt::RDiscountTemplate, Tilt::BlueClothTemplate, Tilt::KramdownTemplate, Tilt::MarukuTemplate]
md           : [Tilt::RedcarpetTemplate, Tilt::RedcarpetTemplate::Redcarpet2, Tilt::RedcarpetTemplate::Redcarpet1, Tilt::RDiscountTemplate, Tilt::BlueClothTemplate, Tilt::KramdownTemplate, Tilt::MarukuTemplate]
textile      : [Tilt::RedClothTemplate]
rdoc         : [Tilt::RDocTemplate]
wiki         : [Tilt::WikiClothTemplate, Tilt::CreoleTemplate]
creole       : [Tilt::CreoleTemplate]
mediawiki    : [Tilt::WikiClothTemplate]
mw           : [Tilt::WikiClothTemplate]
yajl         : [Tilt::YajlTemplate]
ad           : [Tilt::AsciidoctorTemplate]
adoc         : [Tilt::AsciidoctorTemplate]
asciidoc     : [Tilt::AsciidoctorTemplate]
html         : [Tilt::PlainTemplate]
~~~

Wow. That's quite an offering. Let's see tilt in action and
let's unroll the six somewhat obfuscated lines of Almost Sinatra to see its full inner beauty.

`almost_sinatra_ii.rb`:

~~~
# Note: to keep it simple; the inline template machinery e.g. S=/@@ *([^\n]+)\n(((?!@@)[^\n]*\n)*)/m
        and the before, configure 'n' friends blocks got dropped

## line 1
# %w.rack tilt date INT TERM..map{|l|trap(l){$r.stop}rescue require l};$u=Date;$z=($u.new.year + 145).abs;puts "== Almost Sinatra/No Version has taken the stage on #$z for development with backup from Webrick"

require 'rack'
require 'tilt'

trap( 'INT' )  { $server.stop }  # rename $r to $server
trap( 'TERM' ) { $server.stop }

$port = 4567              # rename $z to $port

puts "== Almost Sinatra II has taken the stage on #{$port} for development with backup from Webrick"


## line 2
# $n=Module.new{extend Rack;a,D,S,q=Rack::Builder.new,Object.method(:define_method),/@@ *([^\n]+)\n(((?!@@)[^\n]*\n)*)/m

$n = Module.new do
  app = Rack::Builder.new      # rename a to app
  req = nil                    # rename q to req

## line 3
# %w[get post put delete].map{|m|D.(m){|u,&b|a.map(u){run->(e){[200,{"Content-Type"=>"text/html"},[a.instance_eval(&b)]]}}}}

  ['get','post','put','delete'].each do |method|
    define_method method do |path, &block|
      app.map( path ) do
        run ->(env){ [200, {'Content-Type'=>'text/html'}, [app.instance_eval( &block )]]}
      end
    end
  end

## line 4
# Tilt.mappings.map{|k,v|D.(k){|n,*o|$t||=(h=$u._jisx0301("hash, please");File.read(caller[0][/^[^:]+/]).scan(S){|a,b|h[a]=b};h);v[0].new(*o){n=="#{n}"?n:$t[n.to_s]}.render(a,o[0].try(:[],:locals)||{})}}

  Tilt.mappings.each do |ext, engines|          # rename k to ext and v to engines
    define_method ext do |text, *args|          # rename n to text and o to args
      template = engines[0].new(*args) do
        text
      end
      locals = (args[0].respond_to?(:[]) ? args[0][:locals] : nil) || {}    # was o[0].try(:[],:locals)||{}
      template.render( app, locals )
    end
  end

## line 5
# %w[set enable disable configure helpers use register].map{|m|D.(m){|*_,&b|b.try :[]}};END{Rack::Handler.get("webrick").run(a,Port:$z){|s|$r=s}}

  # was END { ... }; change to run! method
  define_method 'run!' do
    Rack::Handler.get('webrick').run( app, Port:$port ) {|server| $server=server }
  end

## line 6
# %w[params session].map{|m|D.(m){q.send m}};a.use Rack::Session::Cookie;a.use Rack::Lock;D.(:before){|&b|a.use Rack::Config,&b};before{|e|q=Rack::Request.new e;q.params.dup.map{|k,v|params[k.to_sym]=v}}}

  ['params','session'].each do |method|
    define_method method do
      req.send method
    end
  end

  app.use Rack::Session::Cookie
  app.use Rack::Lock
  app.use Rack::Config do |env|
    req = Rack::Request.new( env )
  end
end # Module.new
~~~

All the tilt magic happens in line 4. Almost Sinatra will
define a method for every format so you can use, for example:

~~~
markdown 'Strong emphasis, aka bold, with **asterisks** or __underscores__.'
~~~

or

~~~
erb "Hello <%= name %>!", locals: { name: params['name'] }
~~~

Let's try the embedded ruby (erb) example by hand:

~~~
require 'tilt'

template =  Tilt::ErubisTemplate.new do        # Step 1: create a template
              "Hello <%= name %>!"
            end

puts template.render( self, name: 'World' )    # Step 2: render the template
                                               # There's no step 3 ;-)
~~~

Or to more closely reflect the Almost Sinatra setup:

~~~
require 'tilt'

text   = "Hello <%= name %>!"
params = { name: 'World' }
args   = [ { locals: { name: params[:name] } } ]

template =  Tilt::ErubisTemplate.new do
              text
            end

locals = args[0][:locals]
puts template.render( self, locals )
~~~

Resulting in:

~~~
Hello World!
~~~


The proof of the pudding. To wrap up let's fire up Almost Sinatra II with an example
app that renders a document (template) in markdown and
another with embedded ruby (erb).

`example.rb`:

~~~
require_relative 'almost_sinatra_ii'

include $n    # include "anonymous" Almost Sinatra DSL module


get '/' do
  markdown <<EOS
## Welcome to Tilt

A Generic interface to multiple template engines

Try:
- [Say hello!](/hello?name=Tilt)

Powered by Almost Sinatra II (#{Time.now})
EOS
end


get '/hello' do
  erb "Hello <%= name %>!", locals: { name: params['name'] }
end


run!
~~~

Use

~~~
$ ruby ./example.rb
~~~

to startup the example app. Resulting in:

~~~
== Almost Sinatra II has taken the stage on 4567 for development with backup from Webrick
[2015-04-18 14:42:21] INFO  WEBrick 1.3.1
[2015-04-18 14:42:21] INFO  ruby 2.1.4 (2014-10-27) [i686-linux]
[2015-04-18 14:42:21] INFO  WEBrick::HTTPServer#start: pid=3955 port=4567
~~~

Open up your browser @ `http://localhost:4567` or `http://localhost:4567/hello?name=Tilt`.
Voila.


## Learn More

Almost Sinatra

* home     :: [github.com/rkh/almost-sinatra](https://github.com/rkh/almost-sinatra)
* article  :: [Decoding "Almost Sinatra"](https://robm.me.uk/2013/12/13/decoding-almost-sinatra.html), by Rob Miller
* article  :: [Code Safari: Almost Sinatra, Almost Readable](http://www.sitepoint.com/code-safari-almost-sinatra-almost-readable/), by Xavier Shay

Tilt

* home     :: [github.com/rtomayko/tilt](https://github.com/rtomayko/tilt)
* gem      :: [rubygems.org/gems/tilt](https://rubygems.org/gems/tilt)
* rdoc     :: [rubydoc.info/gems/tilt](http://rubydoc.info/gems/tilt)
