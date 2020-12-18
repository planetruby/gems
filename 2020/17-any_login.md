# Day 17 - any_login Gem - Speed Up Testing Allowing One-Click Turbologins® Without Passwords as Any User in the System. Switching User (Accounts) Made Easy (and Faster)


Written by {% avatar igorkasyanchuk %} [Igor Kasyanchuk](https://github.com/igorkasyanchuk)

_A web and mobile developer from the Ukraine at [Rails Jazz](https://www.railsjazz.com). Maintains among other gems [rails_db](https://rubygems.org/gems/rails_db), [log_analyzer](https://rubygems.org/gems/log_analyzer), [active_storage_validations](https://rubygems.org/gems/active_storage_validations)._



## What's any_login?

The `any_login` gem is a favorite of our quality assurance (QA) team
and I can't imagine any project without it.

`any_login` lets you quickly switch users (with one-click Turbologins® without passwords)
and log in as any user in your app (in development mode).



## Installation

If you're using Devise (or other supported pre-configured
auth libraries incl. Authlogic, Clearance or Sorcery that work out-of-the-box),
for example,
you can add the `any_login` gem, add the `any_login_here` helper to your page
layout and you're up and running.


**Step 1: Add `any_login` to your Gemfile**

``` ruby
gem 'any_login'
```

and run in your console:

```
$ bundle install
```


**Step 2: Add the `any_login_here` helper to your Layout**

In your application layout, for example `app/views/layouts/application.html.erb`,
add the following snippet in the bottom of the page:

``` erb
<%= any_login_here   if defined?(AnyLogin) %>
```

That's it.


Open up your app in the browser
and on the bottom left corner,
you will see the semi-transparent
one-click Turbologins® user icon.
Click on it and now you can select any user
to log in without a password.




### Customization

If you want to customize `any_login` run in your console:

```
$ rails g any_login initializer
```


It will create the initializer
file `config/initializers/any_login.rb`
that lets you use a variety of customization options.
Below you can see an example of
a configuration file (`config/initializers/any_login.rb`):

```ruby
if defined?(AnyLogin)
  AnyLogin.setup do |config|
    # # provider (:devise, :authlogic, sorcery, clearance).
    # #   Provider can be identified automatically based on your Gemfile
    # config.provider = nil

    # # enabled or not
    # config.enabled = Rails.env.to_s == 'development'
    config.enabled = Rails.env.development?

    # # Account, User, Person, etc
    # config.klass_name = 'User'

    #  # .all, .active, .admins, .groped_collection, etc ...
    #  #   need to return an array (or hash with arrays) of users
    config.collection_method = :any_login_by_role

    # # to format user name in dropdown list
    config.name_method = proc { |e| [
        (e.client? ?
        "#{e.email} -- #{e.subscription_plan.try(:en_name)}"
        " -- photos: #{e.own_photos.count} -- #{e.full_name}" :
        "#{e.email} -- #{e.full_name}"),
        e.id] }

    # # after logging in redirect user to path
    # config.redirect_path_after_login = :root_path

    # # login on select change event OR click on button, or BOTH
    # config.login_on = :both

    # # position of any_login box top_left, top_right, bottom_left, bottom_right
    # config.position = :bottom_left

    # # label on Login button
    # config.login_button_label = 'Login'

    # # prompt message in select
    # config.select_prompt = "Select #{AnyLogin.klass_name}"

    # # show any_login box by default
    # config.auto_show = false

    # # limit, integer or :none
    # config.limit = 10

    # # Enable http basic authentication
    # config.http_basic_authentication_enabled = false

    # # Enable http basic authentication
    # config.http_basic_authentication_user_name = 'any_login'

    # # Enable http basic authentication
    # config.http_basic_authentication_password = 'password'

    # # Use controller proc condition
    # config.verify_access_proc = proc { |controller| true }
  end
end
```


Tip:  If you want to completely disable the `any_login` gem
in production add the following code in your `config/environments/production.rb` file.

```ruby
AnyLogin.setup do |config|
  config.enabled = false
end
```


Happy user (account) switching with one-click Turbologins® without passwords!


## Find Out More

### References

- Home :: [github.com/igorkasyanchuk/any_login](https://github.com/igorkasyanchuk/any_login)
- Gem :: [rubygems.org/gems/any_login](https://rubygems.org/gems/any_login)
- Docs :: [rdoc.info/gems/any_login](https://rdoc.info/gems/any_login)
- Live Demo :: [Any Login Live Dummy Demo App](https://any-login.herokuapp.com/)
- Live Demo App Source :: [github.com/igorkasyanchuk/any_login_test](https://github.com/igorkasyanchuk/any_login_test)
