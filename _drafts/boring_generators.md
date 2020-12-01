# Day 11 - Boring Generators - aims to make your development faster by delegating boring setups to us.

Written by [Abhay Nikam](https://twitter.com/abhaynikam13) from [Boring Generators](https://www.boringgenerators.com) {% avatar abhaynikam %}

## Problem Statement
Have you ever started a hobby/adventure project and spent half of the initial time in boring configurations and installation of gems and libraries? Yeah. I have been in similar situation. When you start a project you have tons of thought to be processed and ideas that needs attention than working on boring setup and I wanted to counter this problem.

The easiest solution to such problems is to create a simple application template bundled with all features you need and use it every time you start a new project. But I see some drawbacks to this solution.

- You need to keep the template updated with all the new changes happening. Example: Ruby 3.0 released. You need to update Ruby version and make template compatible with new version.
- What if you aren’t starting a new project? You can’t spends copy-pasting stuff which you did add a year ago in template.

## Motivation
I recently wrote a blog on [testing rails generators](https://www.abhaynikam.me/posts/rails_generator_testing/) which somewhat speaks about the motivation of working on generators. We use Rails generators all the time. From rails new <app_name> to rails generate model <model_name>. These generators makes using Rails so easy and faster that most of the people are able to concentrate their time on tacking the actual problem the application is supposed to handle. I did some work around Action Text and Action Mailbox generators myself.

Boring generators uses the same Rails generator and help you to easily configure the application. We would keep the generator updated as well as you could use it mid-way in your projects.

## How to use boring generator?
- Add the gem to your development group.

```ruby
# Gemfile
gem 'boring_generators', group: :development
```

- Run the generator you want from console.
```
> rails generate boring:tailwind:install
```

- NOTE: We are aiming to add a CLI for boring generators so that you don’t need to add gem in Gemfile.

## Changelog and generators supported
- [Changelog](https://github.com/abhaynikam/boring_generators/blob/master/CHANGELOG.md)
- [Generators Supported](https://github.com/abhaynikam/boring_generators#usage)

### References

- home :: [github.com/abhaynikam/boring_generators](https://github.com/abhaynikam/boring_generators)
- gem :: [rubygems.org/gems/boring_generators](https://rubygems.org/gems/boring_generators)
- article :: [Introducing Boring Generators](https://www.abhaynikam.me/posts/introducing_boring_generators/)
