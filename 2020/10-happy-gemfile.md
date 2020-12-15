# Day 10 - happy_gemfile Gem - Stop House Keeping Your Gemfile. (Auto-)Format Your Gems Sorted from A to Z and by Groups. (Auto-)Clean up the Clutter. Happy Gemfile Day!


Written by {% avatar MikeRogers0 %} [Mike Rogers](https://github.com/MikeRogers0)

_A Rubyist, a menace, a web hygienist. Working on [Typo CI](https://github.com/marketplace/typo-ci), a spellchecker for Pull Requests. Looking for help with [Ruby Starter Kits](https://github.com/Ruby-Starter-Kits/), a way for people to get Ruby up and running with just docker._



## What is happy_gemfile?

The [happy_gemfile](https://rubygems.org/gems/happy_gemfile) is an awesome gem that sorts your projects Gemfile. It strips comments, organises the groups & then alphabetises everything.

It's kind of a radical, but I've really started to like this approach.

## How do you use it?

```bash
$ gem install happy_gemfile
$ happy_gemfile all
```

Install the gem onto your machine, then run `happy_gemfile all` in your terminal and you're good, that will rewrite your Gemfile to be super organised!

## Why would you want to sort the Gemfile?

The Gemfile is an powerful file, it tells bundler which gems you'd like installed, the version number you'd be happy with & possibly if you only want to install that gem in development.

They're great, but also wildly different between projects. Some projects may add lots of comments, some may group gems by category with nice headings & others may just drop in required gems in an adhoc manor. In most cases, any attempt at organising is very short lived.

However, I've recently started ordering my gems alphabetically and I've really enjoyed how much _I don't have to think about my Gemfile_, I can just run a command and move on.

## What does the end result look like?

Your average boilerplate Rails Gemfile ends up being a lot more cut down, initially it might throw you off that `rails` is in the middle of the pack, but I think it does feel more easier to cognitively digest.

```ruby
source 'https://rubygems.org'
git_source(:github) { |repo| "https://github.com/#{repo}.git" }
ruby '2.7.2'

gem 'bootsnap', '>= 1.4.2', require: false
gem 'jbuilder', '~> 2.7'
gem 'puma', '~> 4.1'
gem 'rails', '~> 6.0.3', '>= 6.0.3.4'
gem 'sass-rails', '>= 6'
gem 'sqlite3', '~> 1.4'
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]

group :development, :test do
  gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
end

group :development do
  gem 'listen', '~> 3.2'
  gem 'spring'
  gem 'spring-watcher-listen', '~> 2.0.0'
  gem 'web-console', '>= 3.3.0'
end
```



## Are their any drawbacks?

Bundler loads the gems in the order they're appear in the Gemfile, so if a gem requires another to be present without explicitly requiring it, it could cause problems. However, I think this is a problem of the past, as gems are much more well written today compare to 10 years ago.

## How does this change adding new gems?

As a result of not having to worry about the ordering of my Gemfile, I've started using [`$ bundle add`](https://bundler.io/man/bundle-add.1.html) when adding my gems. This just adds the new gem to the bottom of the file, so I do need to rerun `happy_gemfile all` before committing it.


## Find Out More

### References

- Home :: [github.com/MainShayne233/happy_gemfile](https://github.com/MainShayne233/happy_gemfile)
- Gem :: [rubygems.org/gems/happy_gemfile](https://rubygems.org/gems/happy_gemfile)
- Docs :: [rdoc.info/gems/happy_gemfile](https://rdoc.info/gems/happy_gemfile)
- Screencast :: [happy_gemfile - Organise your Gemfile](https://www.youtube.com/watch?v=oQ-gYHOAf00)


