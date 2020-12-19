# Day 19 - quik Gem - Quick Starter Template Script Wizard - The Missing Code Generator and Project Scaffolder for Gems, Sinatra, Jekyll 'n' More


Written by {% avatar geraldb %} [Gerald Bauer](https://github.com/geraldb)



Let's start with a quiz:

Q: How do you get started with creating a new gem?

- (A) From zero / scratch ;-)
- (B) Using [bundler](https://bundler.io/v2.2/man/bundle-gem.1.html) with `$ bundle gem`
- (C) Using [quik](https://github.com/quikstart/gem-starter-template) with `$ quik new gem`
- (D) Using [hoe](https://github.com/seattlerb/hoe) with `$ sow` (incl. with hoe rake tasks gem)
- (E) Other (Please Tell).


Q: How do you get started with creating a new sinatra app or service?

- (A) From zero / scratch ;-)
- (B) Using [padrino](http://padrinorb.com/guides/generators/projects) with `$ padrino g project`
- (C) Using [quik](https://github.com/quikstart/sinatra-starter-template) with `$ quik new sinatra`
- (D) Other (Please Tell).


Q: How do you get started with creating a new jekyll theme?

- (A) From zero / scratch ;-)
- (B) Using [jekyll](http://jekyllrb.com/docs/themes/#creating-a-gem-based-theme) with `$ jekyll new-theme`
- (C) Using [quik](https://github.com/quikstart/jekyll-starter-theme) with `$ quik new jekyll`
- (D) Other (Please Tell).



## One quick starter to rule them all?

Let's welcome the quik gem and command line tool.

The idea:  Many starter templates / boilerplates
are ready-to-fork GitHub repos.
Why not turn GitHub repos into quik starter templates?!
Let's do it in 1-2-3 steps.


## Step 1:  Download Single-File Quik Starter (.ZIP) Archive

Did you know? You can download GitHub repos without git?
That is, you can download always a single-file archive (.ZIP)
that gets (auto-)built by GitHub packaging
the latest source files (from the master/main branch).


Let's look inside the gem starter example - `gem-starter-template.zip`:

```
lib/
  $filename$.rb
  $filename$/
    version.rb
test/
  helper.rb
  test_version.rb
.gitignore
HISTORY.md
Manifest.txt
README.md
Rakefile
```
(Source: [quikstart/gem-starter-template](https://github.com/quikstart/gem-starter-template))



## Step 2: Parameterize Files - Use a Template Language


Q: What template language would you choose?

- (A) Use Embedded Ruby (ERB)
- (B) Use Liquid
- (C) Other (Please Tell).


For example, let's parametrize the `lib/hello/version.rb` script
for use in a gem template / scaffold:

``` ruby
module Hello

  MAJOR = 0
  MINOR = 0
  PATCH = 1
  VERSION = [MAJOR,MINOR,PATCH].join('.')

  def self.version
    VERSION
  end

  def self.banner
    "hello/#{VERSION} on Ruby #{RUBY_VERSION} (#{RUBY_RELEASE_DATE}) [#{RUBY_PLATFORM}]"
  end

end  # module Hello
```



Q: What template language would you choose?

A: Yes, let's use a new (simpler) template language (e.g. `$name$`)!

Example - `lib/$filename$/version.rb`:

``` ruby
module $module$

  MAJOR = 0
  MINOR = 0
  PATCH = 1
  VERSION = [MAJOR,MINOR,PATCH].join('.')

  def self.version
    VERSION
  end

  def self.banner
    "$name$/#{VERSION} on Ruby #{RUBY_VERSION} (#{RUBY_RELEASE_DATE}) [#{RUBY_PLATFORM}]"
  end

end  # module $module$
```

(Source: [quikstart/gem-starter-template/template/lib/$filename$/version.rb](https://github.com/quikstart/gem-starter-template/blob/master/template/lib/%24filename%24/version.rb))



Why not (re)use Embedded Ruby (ERB) or Liquid?

A new meta template template language is:

- Simpler  -- works inside filenames too ;-) e.g. `lib/$filename$/version.rb`
- Shorter  -- less typing (plus: no worries about whitespace)

**Most Important:** A new meta template template language
is "orthogonal" to Embedded Ruby (ERB) and Liquid.
It let's you parameterize Embedded Ruby (ERB) or Liquid templates too -
no need for escaping or "raw" blocks etc.


```
module $module$     | module <%= module %>  | module {{ module }}  | module Hello
  ...               |  ...                  |   ...                |   ...
end                 | end                   | end                  | end
```



## Step 3: What's Missing? All together Now - Automate with a Script


Q: What script language would you choose?

- (A) Use Ruby
- (B) Use Python
- (C) Use JavaScript
- (D) Other (Please Tell).


Let's use Ruby ;-) with a new wizard mini language,
that is, a domain-specific language (DSL).

Example - `scripts/gem.rb`:

``` ruby
say "Hello from the gem quick starter wizard script"

name  = ask "Name of the gem", "hola"

def make_module( name )
   ...
end

module_name = ask "Module name of the gem", make_module( name )


## use template repo e.g. github.com/quikstart/gem-starter-template

use "quikstart/gem-starter-template"

config do |c|
  c.name     = name
  c.filename = name     ## for now assume name is 1:1 used as filename
  c.module   = module_name

  c.date     = Time.new.strftime("%Y-%m-%d")  ## e.g. use like $date$  => 2015-08-27
end
```

(Source: [quikstart/scripts/gem.rb](https://github.com/quikstart/scripts/blob/master/gem.rb))


Voila. That's it.




## Appendix: `quik help`  - Quik Starter Commands

```
$ quik --help      # or
$ qk -h
```

Resulting in:

```
NAME
    qk/quik - ruby quick starter template script wizard .:. the missing code generator

SYNOPSIS
    quik [global options] command [command options] [arguments...]

VERSION
    0.3.0

GLOBAL OPTIONS
    --help            - Show this message
    --verbose         - (Debug) Show debug messages
    --version         - Display the program version

COMMANDS
    list, ls, l - List ruby quick starter scripts
    new, n      - Run ruby quick starter script

    help        - Shows a list of commands or help for one command
```

### `quik ls` - List Quik Starter Wizards

Use:

```
$ quik list    # or
$ quik ls      # or
$ quik l       # or
$ qk l
```

Resulting in:

```
  1..gem        .:.  Gem Quick Starter Template
  2..gem-hoe    .:.  Gem Quick Starter Template (Hoe Classic Edition)
  3..sinatra    .:.  Sinatra Quick Starter Template
...
```


### `quik new` - New Wizard Quik Start

To run a quick starter template wizard script
to download and install (unzip/unpack) a template archive and configure
the code ready-to-use. Try:

```
$ quik new gem    # or
$ quik n gem      # or
$ qk n gem
```

This will download the `gem.rb` wizard script
from the [Scripts](https://github.com/quikstart/scripts) repo
and run through all steps e.g.:

```
Welcome, to the gem quick starter script.

Q: What's your gem's name? [hola]:   hello
Q: What's your gem's module? [Hola]: Hello

Thanks! Ready-to-go. Stand back.

  Downloading Gem Starter Template...
  Setting up Starter Template...
  ...
Done.
```

That's it. Now the gem starter code is ready in the `hello`
folder.


## Find Out More

### References

* Home :: [github.com/quikstart/quik](https://github.com/quikstart/quik),
* Gem  :: [quik](https://rubygems.org/gems/quik),
* Docs :: [quik](http://rubydoc.info/gems/quik)
