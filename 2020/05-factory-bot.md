# Day 5 - factory_bot Gem - "Hey, Make Me a User with an Email and Password" - Setup Factories That Make You Fake Objects with Fake Data for Testing

Written by {% avatar JasonSwett %} [Jason Swett](https://github.com/jasonswett)

_Blogs about [Ruby on Rails Testing](https://www.codewithjason.com/), speaks at international conferences and hosts the [Rails with Jason Podcast](https://www.codewithjason.com/rails-with-jason-podcast/)._



## Creating test data without headaches

`factory_bot` is a fixtures replacement with a straightforward definition syntax, support for multiple build strategies (saved instances, unsaved instances, attribute hashes, and stubbed objects), and support for multiple factories for the same class (user, admin_user, and so on), including factory inheritance.

There are three steps I go through to set up Factory Bot in a Rails application.

1.  Install the `factory_bot_rails` gem
2.  Set up one or more factory definitions
3.  Add the Factory Bot syntax methods to my `rails_helper.rb` file

## Install the factory\_bot\_rails gem

The first thing I do is to include the [factory\_bot\_rails](https://github.com/thoughtbot/factory_bot_rails) gem (not the `factory_bot` gem) in my `Gemfile`. I include it under the `:development, :test` group.

Here's a sample Gemfile from a project with only the default gems plus a few that I added for testing.

Remember that after you add a gem to your `Gemfile` you'll need to run `bundle install` in order to actually install the gem.

```ruby
source 'https://rubygems.org'
git_source(:github) { |repo| "https://github.com/#{repo}.git" }

ruby '2.7.0'

# Bundle edge Rails instead: gem 'rails', github: 'rails/rails'
gem 'rails', '~> 6.0.2', '>= 6.0.2.2'
# Use postgresql as the database for Active Record
gem 'pg', '>= 0.18', '< 2.0'
# Use Puma as the app server
gem 'puma', '~> 4.1'
# Use SCSS for stylesheets
gem 'sass-rails', '>= 6'
# Transpile app-like JavaScript. Read more: https://github.com/rails/webpacker
gem 'webpacker', '~> 4.0'
# Turbolinks makes navigating your web application faster. Read more: https://github.com/turbolinks/turbolinks
gem 'turbolinks', '~> 5'
# Build JSON APIs with ease. Read more: https://github.com/rails/jbuilder

gem 'devise'

# Reduces boot times through caching; required in config/boot.rb
gem 'bootsnap', '>= 1.4.2', require: false

group :development, :test do
  gem 'pry'
  gem 'rspec-rails'
  gem 'capybara'
  gem 'webdrivers'
  gem 'factory_bot_rails'
end

group :development do
  # Access an interactive console on exception pages or by calling 'console' anywhere in the code.
  gem 'web-console', '>= 3.3.0'
  gem 'listen', '>= 3.0.5', '< 3.2'
  # Spring speeds up development by keeping your application running in the background. Read more: https://github.com/rails/spring
  gem 'spring'
  gem 'spring-watcher-listen', '~> 2.0.0'
end

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]
```

## Set up one or more factory definitions

Factory definitions are kind of the "templates" that are used for generating new objects.

For example, I have a user object that needs an email and a password, then I would create a factory definition saying "hey, make me a user with an email and password". The actual code might look like this:

```ruby
FactoryBot.define do
  factory :user do
    email { 'test@example.com' }
    password { 'password1' }
  end
end
```

Factory Bot is smart enough to know that when I say `factory :user do`, I'm talking about an Active Record class called `User`.

There's a problem with this way of defining my `User` factory though. If I have a unique constraint on the `users.email` column in the database (for example), then I won't ever be able to generate more than one `User` object. The first user's email address will be `test@example.com` (no problem so far) but then when I go to create a second user, its email address will also be `test@example.com`, and if I have a unique constraint on `users.email`, the creation of this second record will not be allowed.

We need a way of making it so the factories' values can be unique. One way, which I've done before, is to append a random number to the end of the email address, e.g. `"test#{SecureRandom.hex}@example.com"`. There's a different way to do it, though, that I find nicer. That way is to use another gem called Faker.


## Add the Factory Bot syntax methods to my rails\_helper.rb file

The syntax for actually using a Factory Bot factory in a test is as follows:

```ruby
FactoryBot.create(:user)
```

There's nothing wrong with this, but I find that these `FactoryBot` are so numerous in my test files that their presence feels a little noisy.

There's a way to make it so that instead we can just write this:

```ruby
create(:user)
```

The way to do that is to add a bit of code to `spec/rails_helper.rb`.

```ruby
RSpec.configure do |config|
  config.include FactoryBot::Syntax::Methods
end
```

(You don't actually add the `RSpec.configure do |config|` to the `spec/rails_helper.rb` file. It's already there. I'm just including it here to show that that's the block inside of which the `config.include FactoryBot::Syntax::Methods` line goes.)

## Find Out More

### References

- home :: [github.com/thoughtbot/factory_bot](https://github.com/thoughtbot/factory_bot)
- gem :: [rubygems.org/gems/factory_bot](https://rubygems.org/gems/factory_bot)
- article :: [Rails testing "hello world"](https://www.codewithjason.com/rails-testing-hello-world-using-rspec-capybara/)
