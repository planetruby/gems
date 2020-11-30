# Day 8 - ActsAsTenant - Row-level multitenancy for Rails

Written by [Chris Oliver](https://twitter.com/excid3) from [GoRails](https://gorails.com/) {% avatar excid3 %}

## Multitenancy made easy with ActsAsTenant

`multitenancy` is a term used to describe a single software application that handles user (tenant) data separately.

>The term "software multitenancy" refers to a software architecture in which a single instance of software runs on a server and serves multiple tenants. Systems designed in such manner are often called shared. A tenant is a group of users who share a common access with specific privileges to the software instance.
>[Wikipedia](https://en.wikipedia.org/wiki/Multitenancy)

ActsAsTenant implements row-level multitenancy for Rails. It's a popular, well loved gem that takes advantage of several features in Rails to make multitenancy easy to use.

## What are the different multitenant strategies?

Row-level multitenancy is when you add a tenant_id column to all the models that should be scoped to a tenant. When you query or insert new records, a tenant_id is always required to separate the data between tenants.

Another approach is using database schemas for multitenancy. Postgres databases have schemas with tables inside them. Each tenant gets its own schema with their own unique tables. This is great for separating out data but makes it complicated to run migrations and scale up as you have more tenants.

## Using the gem

After you install ActsAsTenant, you'll need to do two things:

#### 1. Setup your models

You might already have a tenant model already like Account, Team, Organization, User, etc. You'll simply need to add an association to each of your models that you want scoped by ActsAsTenant.

```ruby
rails g scaffold Account name subdomain domain
rails g scaffold Project account_id:integer name description:text
```

Once created, you can edit your models to use acts_as_tenant.

```ruby
class Project
  # This adds a belongs_to :account behind the scenes so you don't have to
  acts_as_tenant(:account)
end
```

#### 2. Configure your controllers

You might notice we added `subdomain` and `domain` fields to our Account tenant model. We can use these fields to automatically set the current tenant for a request based upon the subdomain or domain in the URL.

To set the current tenant automatically, we just need to add one line to our ApplicationController

```ruby
class ApplicationController < ActionController::Base
  set_current_tenant_by_subdomain_or_domain(:account, :subdomain, :domain)
end
```

This will set the current which you can access anywhere by calling `ActsAsTenant.current_tenant`

Once a tenant is set, calling `Project.all` will return only the projects for the current tenant. It does this by adding a `default_scope` to automatically filter queries to the current tenant. ActsAsTenant will also automatically set the tenant ID when you call `Project.create(name: "test")`.

These changes make it easy to write code that creates and queries records in the current tenant. You don't have to worry about forgetting to set a tenant ID on a record, it's all done for you.

## Find Out More

### References

- Home :: [github.com/ErwinM/acts_as_tenant](https://github.com/ErwinM/acts_as_tenant)
- Gem :: [rubygems.org/gems/acts_as_tenant](https://rubygems.org/gems/acts_as_tenant)
- Screencast :: [How to use ActsAsTenant](https://www.youtube.com/watch?v=BIyxM9f8Jus)
