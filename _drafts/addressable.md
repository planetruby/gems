# Day 19 - addressable gem - the URI implementation that should be standard

Written by {% avatar soulcutter %} [Bradley Schaefer](http://soulcutter.com/) 

_Engineering Manager at [Stitch Fix](https://stitchfix.com), a dad, a son, a brother, Team Oxford Comma, and a long-time Ruby programmer._

https://github.com/sporkmonger/addressable

## What is Addressable?

Addressable is an implementation of URIs, including a parser, an object representing a URI, and URI Templates ([RFC 6570](https://www.rfc-editor.org/rfc/rfc6570.txt)). This gem has quietly become the [25th most-downloaded ruby gem](https://rubygems.org/stats?page=3) as-of the writing of this article, with over 283 million downloads. Nevertheless many experienced Rubyists aren't familiar with this library - it's a true hidden gem.

 because Ruby *itself* comes with a [different URI implementation in the standard library](https://ruby-doc.org/stdlib-2.7.2/libdoc/uri/rdoc/URI.html).

### How is it so popular without people knowing about it?
  
What makes `addressable` popular is that it's a low-level library that is a [direct dependency of many highly popular gems](https://rubygems.org/gems/addressable/reverse_dependencies) as well as an indirect dependency of Rails itself. There is a very high chance that you already rely on this gem in code you've written by virtue of popular gems that use it under the hood.

To explains why people may not be familiar with it, Ruby *itself* comes with a [different URI implementation in the standard library](https://ruby-doc.org/stdlib-2.7.2/libdoc/uri/rdoc/URI.html). Because `addressable` isn't part of the _standard_ library, most tutorials and blog posts dealing with URIs default to using the standard library version rather than introducing `addressable` as an unexplained dependency. Fair enough.


## Why use Addressable?

Avoiding dependencies has many virtues, however I will enumerate reasons that make a compelling case for using addressable's implementation over the standard library implementation in your own code.


### It's Stable & Reliable

This gem has been around since 2009 and yet has seen only 38 different versions released to date. Don't get scared away by the glacial pace of its development; the reason it isn't more-active is because it has a small, clear, and precise scope. There's no strong pressure for the library to change, it is a 'complete' project. Given how many popular gems depend on `addressable`, its high stability should be valued as something that has a low risk of breaking all the code that uses it. 






```ruby
# file.rb

require "uri"

URI("https://www.rfc-editor.org/rfc/rfc3986.txt")
```

**Note:** While this gem is old (released in 209) and infrequently updated, don't be scared off thinking that this project is abandoned -- it's simply "done" and works great.

## Find Out More

### References

* home  :: [github.com/basecamp/local_time](https://github.com/sporkmonger/addressable)
* gem   :: [rubygems.org/gems/local_time](https://rubygems.org/gems/addressable)
