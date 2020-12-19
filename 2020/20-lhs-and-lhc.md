# Day 20 - `lhs` and `lhc` - Smart HTTP Clients

Written by {% avatar marcoroth %} [Marco Roth](https://github.com/marcoroth)

_Full-Stack Web-Developer based in Basel, Switzerland. Building stuff with Ruby, Rails, Stimulus and JavaScript. [StimulusReflex](https://github.com/hopsoft/stimulus_reflex) core team member_.

## Introduction

I love Ruby and I think Ruby solved a lot of things very elegantly with a ton of features directly built into the standard library. HTTP requests in Ruby with `net/http` are the only thing that never really clicked for me. In fact `net/http` has been there forever, but im my experience it never was a blast to work with.

In 2019 I attended a local Ruby Meetup and heard a talk by [@sebpape](https://twitter.com/sebpape) about "Smart HTTP Clients". In that talk Sebastian talked about how they were building HTTP Clients at [localsearch.ch](https://localsearch.ch) and how their HTTP clients evolved over the years and what kind of issues and pains they were facing.

He showed off the two libraries `lhs` and `lhc` which they've built over the years in order solve the issues they experienced with the mapping between Ruby objects and JSON responses from REST APIs.

Entering `lhs` and `lhc`.

## What is `lhs` and `lhc`

### LHC

LHC (which I believe stands for "[**L**ocal.ch](https://local.ch) **H**TTP **C**lient") is an advanced HTTP client which builds on top of the fast [typhoeus](https://github.com/typhoeus/typhoeus) gem for low level http communication.

It supports a wide range of enhancements like interceptors, exception handling, format handling, accessing response data, configuring endpoints/placeholders and fully compatible, RFC-compliant URL-template support.

### LHS

LHS (which I believe stands for "[**L**ocal.ch](https://local.ch) **H**TTP **S**ervice") is a gem built on top of LHC.
It provides an easy and ActiveRecord-like interface for accessing REST web services.

It offers a lot of features that makes you think that you don't even interact with a remote JSON API-endpoint anymore! It feels like you interact with the database directly.

## Setup and examples

Without further ado let's jump into some spicy examples.

### LHC

The intuitive and straight-forward interface of LHC makes it really easy to execute HTTP requests. After adding the `lhc` gem to your project you can request JSON endpoints like this:

```ruby
feedbacks = LHC.get('https://datastore.com/v1/feedbacks')

feedbacks.data.items[0]
feedbacks.data.items[0].recommended
feedbacks.body
feedbacks.headers
```

You can easily access the JSON data provided in the response and access all the related informations of the response. LHC also works with any other kind of HTTP requests. I just showcase the built-in JSON support here.

There is a similar method for all the HTTP-methods. Here's an example of a simple POST request:

```ruby
LHC.json.post('https://datastore.com/v1/feedbacks', body: { name: 'Hello', recommended: true })
```

While I just want to cover the basic use here there is a lot more features to discover! The gem is really feature loaded. You should checkout the detailed README [here](https://github.com/local-ch/lhc).


### LHS

Let's talk about LHS. I was very excited the first time I saw LHS in action. In order to demonstrate what LHS offers: Let's build a simple ActiveRecord-like wrapper for the request we did in the LHC example above to show off it's capabilities.

After adding the `lhs` gem to your project you can configure the LHC-Client which LHS is going to use. This example uses Rails, but LHS also works fine outside of Rails (for example if you want to create an API-wrapper gem).

Let's define the root URL for our service (this is very helpful if you have different environments):

```ruby
# config/initializers/lhc.rb

LHC.configure do |config|
  config.placeholder(:service, 'https://datastore.com/v1')
end
```

With that we can create our first record:

```ruby
# app/models/feedback.rb

class Feedback < LHS::Record
  endpoint '{+service}/feedbacks'
  endpoint '{+service}/feedbacks/{id}'
end
```

If you believe it or not: with that we just built a very simple ActiveRecord-like wrapper for our API! It lets us fetch, create, update and delete records plus lots more.

Of course this doesn't use any authentication yet, but this will be very easy to add later on.

This allows us now to query the API as such:

```ruby
feedback = Feedback.find(3424)

feedback.recommended
# => true
```

You can also use all the methods you know and love from ActiveRecord:

```ruby
Feedback.all
Feedback.first
Feedback.last
Feedback.find(1, 2, 3)
Feedback.find_by(user_id: 45352)
Feedback.first(params: { recommended: true })
Feedback.where(recommended: true)
```

It's super easy to build, create, update and delete records:


```ruby
Feedback.create(text: 'Hello world')
# POST https://datastore.com/v1/feedbacks

feedback = Feedback.new
feedback.text = 'Hello world'
feedback.recommended = true
feedback.save
# POST https://datastore.com/v1/feedbacks

feedback = Feedback.find(3424)
feedback.update(recommended: true)
# PATCH https://datastore.com/v1/feedbacks/3424

feedback.destroy
# DELETE https://datastore.com/v1/feedbacks/3424
```

I think this is super awesome! You can create API wrappers with that in no time.

With that said, it's a super powerful and easy to use gem. There is a whole lot more to discover, which I can't cover in this showcase here.

Here is a list of more awesome features LHS provides:

* [attribute mapping and nested records](https://github.com/local-ch/LHS#map-complex-data-for-easy-access)
* [scopes](https://github.com/local-ch/LHS#reusedry-where-statements-use-scopes)
* [validations](https://github.com/local-ch/LHS#record-validation)
* [pagination](https://github.com/local-ch/LHS#record-pagination)
* [relations and assoiciations](https://github.com/local-ch/LHS#relations--associations)
* [includes](https://github.com/local-ch/LHS#include-linked-resources-hyperlinks-and-hypermedia)
* [chaining](https://github.com/local-ch/LHS#chain-complex-queries)
* and [a whole lot more](https://github.com/local-ch/LHS#table-of-contents).

## Why and where should I use them?

The next time you have to built against a remote REST API or have the need for a nice but simple and powerful HTTP client think about if LHS or LHC fits for your use case.

LHC is currently my preferred HTTP client and is included in almost every new project I start. If you need a simple, easy-to-use and advanced HTTP client, LHC is a proper fit!

The same applies for LHS. Whether you are going to ...

* ... build a Rails app against a JSON REST service or
* ... create a Ruby client library gem for a JSON API, ...

... I think you aren't going to be disappointed!

Give it a shot, star the projects on GitHub and tell me about your experiences!

## What's next?

I encourage you to checkout the awesome, detailed and very thought-through [READMEs](https://github.com/local-ch/lhs/blob/master/README.md) the team at [local-ch](https://github.com/local-ch) have put together. They contain both simple and advanced examples of how you can use the gems. They even give you the feeling that they thought about every little detail you didn't even know you needed!

## Alternatives

To be fully transparent: There are numerous other gems providing the same or similar functionalities. Choose whatever fits your use case best. I've been really happy with the easiness and intuitiveness of LHC and LHS. In the end it really depends on your use case and your preferences.

I'll list the alternatives that I've came across in alphabetical order:

- [`activeresource`](https://github.com/rails/activeresource)
- [`api_struct`](https://github.com/rubygarage/api_struct)
- [`flexirest `](https://github.com/flexirest/flexirest)
- [`her`](https://github.com/remi/her)
- [`rest-in-peace`](https://github.com/ninech/REST-in-Peace)
- [`spyke`](https://github.com/balvig/spyke)


## Wrapping up

To be honest it has been a blast to work with those gems. I've really learned to love and appreciate the two gems!

I'd like to thank you for reading this showcase! Hit me up on [Twitter](https://twitter.com/marcoroth_) if you got any questions about LHC or LHS.

A special thanks goes out to [@sebpape](https://twitter.com/sebpape) and the team for providing such beautiful and well documented gems! Thank you!


## References

### LHS

- Docs/README :: [github.com/local-ch/lhs](https://github.com/local-ch/lhs/blob/master/README.md)
- Source :: [github.com/local-ch/lhs](https://github.com/local-ch/lhs)
- Gem :: [rubygems.org/gems/lhs](https://rubygems.org/gems/lhs)

### LHC

- Docs/README :: [github.com/local-ch/lhc](https://github.com/local-ch/lhc/blob/master/README.md)
- Source :: [github.com/local-ch/lhc](https://github.com/local-ch/lhc)
- Gem :: [rubygems.org/gems/lhc](https://rubygems.org/gems/lhc)
