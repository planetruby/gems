# Day X - happy_gemfile gem - Organise your Gemfile

Written by [Mike Rogers](https://twitter.com/MikeRogers0) {% avatar MikeRogers0 %}

<iframe width="560" height="315" src="https://www.youtube.com/embed/oQ-gYHOAf00" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## What is happy_gemfile?

The [happy_gemfile](https://rubygems.org/gems/happy_gemfile) is an awesome gem that sorts your projects Gemfile. It strips comments, organises the groups & then alphabetises everything.

It's kind of a radical, but I've really started to like this approach.

## How do you use it?

Install the gem, then run the `happy_gemfile all` command, e.g:

```bash
$ gem install happy_gemfile
$ happy_gemfile all
```

This will rewrite your Gemfile to be super organised!

## Why would you want to sort the Gemfile?

The Gemfile is an powerful file, it tells bundler which gems you'd like installed, the version number you'd be happy with & possibly if you only want to install that gem in development.

They're great, but also wildly different between projects. Some projects may add lots of comments, some may group gems by category with nice headings & others may just drop in required gems in an adhoc manor. In most cases, any attempt at organising is very short lived.

However, I've recently started ordering my gems alphabetically and I've really enjoyed how much _I don't have to think about my Gemfile_, I can just run a command and move on.

## What does the end result look like?

Your average boilerplate Rails Gemfile ends up being a lot more cut down.

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

## Are their any drawbacks?

Bundler loads the gems in the order they're appear in the Gemfile, so if a gem requires another to be present without explicitly requiring it, it could cause problems. However, I think this is a problem of the past, as gems are much more well written today compare to 10 years ago.

## How does this change adding new gems?

As a result of not having to worry about the ordering of my Gemfile, I've started using [`$ bundle add`](https://bundler.io/man/bundle-add.1.html) when adding my gems. This just adds the new gem to the bottom of the file, so I do need to rerun `happy_gemfile all` before committing it.
