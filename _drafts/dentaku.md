# Day 18 - Dentaku gem - parse and evaluate math safely in Ruby

Written by [Jankees van Woezik](https://twitter.com/jankeesvw) from [Stekker.app](https://stekker.app) {% avatar jankeesvw %}

## Evaluate math and logical formulas

This gem is not something you need for every project, but it's a fun one. 

I'm building a Ruby on Rails app for [Stekker.app](https://stekker.app/). Creating invoices for charging sessions of electric vehicles is one of the things we do. Every customer can have a different contract and those contracts can have different pricing structures.

For instance, this is one of our formulas:

```ruby
minutes * costPerMinute + kwhVolume * costPerKwh
```

You probably wonder how that evaluates to a price for a charging session?

## Enter Dentaku

Dentaku means calculator in Japanese 🧮, and that's exactly what this gem does. The readme has some great examples and I'll share some here so you see what it can do.

Let's start with a simple example:

```ruby
calculator = Dentaku::Calculator.new
calculator.evaluate('10 * 2')
#=> 20
```

Okay, that's not hard and you could do the same with `eval` in Plain Ruby _(please don't it's unsafe!)_. Dentaku can do so much more, take this example:

```ruby
calculator = Dentaku::Calculator.new
calculator.evaluate('kiwi + 5', kiwi: 2)
#=> 7
```

So yes, it can handle variables, now take a look at even a more complex example:

```ruby
calc = Dentaku::Calculator.new
calc.store(monthly_income: 50)
need_to_compute = {
  annual_income: "monthly_income * 12",
  income_taxes:  "annual_income / 5"
}
calc.solve!(need_to_compute)
#=> { annual_income: 600, income_taxes: 120 }
```

We have been using this Gem since last year and it has been rock solid!

## Find Out More

I encourage you to [checkout the Readme](https://github.com/rubysolo/dentaku), it has some great examples of things you can do with Dentaku.

### References

- home :: [github.com/rubysolo/dentaku](https://github.com/rubysolo/dentaku)
- gem :: [rubygems.org/gems/dentaku](https://rubygems.org/gems/dentaku)
- docs :: [rubydoc.info/gems/dentaku](https://www.rubydoc.info/gems/dentaku/)
