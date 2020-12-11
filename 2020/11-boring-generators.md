# Day 11 - boring_generators Gem - 10x Development - Speed-Up Your Coding By Automating and Generating Boring Setups


Written by {% avatar abhaynikam %} [Abhay Nikam](https://github.com/abhaynikam)

_A software developer at AngelList. Working on Ruby on Rails, React and GraphQL. Currently learning TypeScript and React Native. Looking for help with [Boring Generators](https://www.boringgenerators.com) and [React Trix Rich Text Editor (RTE)](https://github.com/abhaynikam/react-trix-rte)._



## Problem Statement

Have you ever started a hobby/adventure project and spent half of the initial time in boring configurations and installation of gems and libraries? Yeah. I have been in similar situation. When you start a project you have tons of thought to be processed and ideas that needs attention than working on boring setup and I wanted to counter this problem.

The easiest solution to such problems is to create a simple starter application template bundled with all features you need and use it every time you start a new project. But I see some drawbacks to this solution.

- You need to keep the template updated with all the new changes happening. Example: Ruby 3.0 released. You need to update Ruby version and make the starter template compatible with the new version.
- What if you aren't starting a new project? You can't copy-n-paste stuff from the starter template into a project which you did add a year ago.



## Motivation

I recently wrote a blog on [testing rails generators](https://www.abhaynikam.me/posts/rails_generator_testing/) which somewhat speaks about the motivation of working on generators. We use Rails generators all the time. From `rails new <app_name>` to `rails generate model <model_name>`. These generators makes using Rails so easy and faster that most of the people are able to concentrate their time on tacking the actual problem the application is supposed to handle. I did some work around Action Text and Action Mailbox generators myself.

Boring generators use the same Rails generator machinery and help you to easily configure the application. We would keep the generators updated as well as you could use the generators mid-way in your projects.



## How to use boring generators?

Step 1: Add the gem to your development group:

```ruby
# Gemfile
gem 'boring_generators', group: :development
```

Step 2: Run the generator you want from the console:

```
$ rails generate boring:tailwind:install
```


NOTE: We are aiming to add a command line tool for boring generators so that you don't need to add the gem to the Gemfile.


## Automate, Automate, Automate

Boring - what can I help you setup or build today? -
generators include:

- [Install Tailwind CSS](https://www.boringgenerators.com/blog/2020-10-18-install-tailwind/)
- [Install Bootstrap](https://www.boringgenerators.com/blog/2020-11-15-install-bootstrap/)
- Install JQuery
- Install FontAwesome via Yarn
- Install FontAwesome via RubyGems
- Install Bullet
- Install Audit gems (`bundler-audit`, `ruby_audit`)
- Install Pry gems for easy debugging
- Install Active Storage for Google Cloud Service
- Install Active Storage for AWS
- Install Active Storage for Azure
- Install CircleCI
- Install GitHub Actions
- Install Travis CI
- Install Rubocop
- Install Pundit
- Build Favicon
- [and some more](https://github.com/abhaynikam/boring_generators#usage)


Happy coding!


## Find Out More

### References

- Home :: [github.com/abhaynikam/boring_generators](https://github.com/abhaynikam/boring_generators)
- Gem :: [rubygems.org/gems/boring_generators](https://rubygems.org/gems/boring_generators)
- What's News? :: [Changelog](https://github.com/abhaynikam/boring_generators/blob/master/CHANGELOG.md)
- Article :: [Introducing Boring Generators](https://www.abhaynikam.me/posts/introducing_boring_generators/)
