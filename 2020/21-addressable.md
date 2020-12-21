# Day 21 - addressable Gem - The Uniform Resource Identifier (URI) Class that Should be Standard (Incl. Templates 'n' More)


Written by {% avatar soulcutter %} [Bradley Schaefer](https://github.com/soulcutter)

_Engineering Manager at [Stitch Fix](https://stitchfix.com), a dad, a son, a brother, a long-time Ruby programmer, and Team Oxford Comma. Blogs at [Soulcutter](http://www.soulcutter.com)._


## What is Addressable?

Addressable is an implementation of Uniform Resource Identifiers (URIs), including a parser, an object representing a URI, and URI Templates ([RFC 6570](https://www.rfc-editor.org/rfc/rfc6570.txt)). This gem has quietly become the [25th most-downloaded ruby gem](https://rubygems.org/stats?page=3) as-of the writing of this article, with over 283 million downloads. Nevertheless many experienced Rubyists aren't familiar with this library - it's a true hidden gem.

### How is it so popular without people knowing about it?

What makes `addressable` popular is that it's a low-level library that is a [direct dependency of many highly popular gems](https://rubygems.org/gems/addressable/reverse_dependencies) as well as an indirect dependency of Rails itself. There is a very high chance that you already rely on this gem in code you've written by virtue of popular gems that use it under the hood.

This explains why people may not be familiar with it, Ruby *itself* comes with a [different URI implementation in the standard library](https://ruby-doc.org/stdlib-2.7.2/libdoc/uri/rdoc/URI.html). Because `addressable` isn't part of the _standard_ library, most tutorials and blog posts dealing with URIs default to using the standard library version rather than introducing `addressable` as an unexplained dependency. Fair enough.


## Why use Addressable?

Avoiding dependencies has many virtues, however I will enumerate reasons that make a compelling case for using addressable's implementation over the standard library implementation in your own code.


### It's Stable 'n' Reliable

This gem has been around since 2009 and yet has seen only 38 different versions released to date. Don't get scared away by the glacial pace of its development; the reason it isn't more-active is because it has a small, clear, and precise scope. There's no strong pressure for the library to change, it is a 'complete' project. Given how many popular gems depend on `addressable`, its high stability should be valued as something that has a low risk of breaking any code that uses it.

### Better Ergonomics for Creating URIs

### URI instantiation wrinkles

```ruby
# uri_tests.rb

require "uri" # standard library
require "addressable/uri"

standard_uri = URI.parse("https://www.rfc-editor.org/rfc/rfc3986.txt")
# => #<URI::HTTPS https://www.rfc-editor.org/rfc/rfc3986.txt>

addressable_uri = Addressable::URI.parse("https://www.rfc-editor.org/rfc/rfc3986.txt")
# => #<Addressable::URI:0x1a4 URI:https://www.rfc-editor.org/rfc/rfc3986.txt>
```

So far so good. There *are*, however, differences in behavior with these forms of instantiation.

#### Conversion method vs Copy constructor

Take this example:

```ruby
# uri_tests.rb

URI.parse(standard_uri)
# URI::InvalidURIError: bad URI(is not URI?): #<URI::HTTPS https://www.rfc-editor.org/rfc/rfc3986.txt>
#   from /Users/soulcutter/.asdf/installs/ruby/2.7.2/lib/ruby/2.7.0/uri/rfc3986_parser.rb:18:in `rescue in split'
# Caused by NoMethodError: undefined method `to_str' for #<URI::HTTPS https://www.rfc-editor.org/rfc/rfc3986.txt>

# You can, however, use the Kernel::URI(uri) conversion method
standard_uri = URI("https://www.rfc-editor.org/rfc/rfc3986.txt") # => => #<URI::HTTPS https://www.rfc-editor.org/rfc/rfc3986.txt>
standard_uri2 = URI(standard_uri) # => #<URI::HTTPS https://www.rfc-editor.org/rfc/rfc3986.txt>
standard_uri2.equal?(standard_uri) # => true

# With Addressable, the parse method acts as a copy constructor
addressable_uri = Addressable::URI.parse("https://www.rfc-editor.org/rfc/rfc3986.txt")
addressable_uri2 = Addressable::URI.parse(addressable_uri) # => #<Addressable::URI:0x654 URI:https://www.rfc-editor.org/rfc/rfc3986.txt>
addressable_uri2.equal?(addressable_uri) # => false
```

There are tradeoffs with copying objects vs returning the same instance. Both `URI`s are *mutable* objects, meaning they have *state* that can be changed. When two variable names point to the same instance of an object, changing the state of an instance through one variable name also changes the same object pointed to by the other variable name. I have seen this cause many bugs over the years in places where this behavior was accidental. Copying the object into a variable via `Addressable::URI.parse(uri)` means that the original and the copy variables are not tied together, making the behavior of changing one variable more-predictable.

Copying objects, on the other hand, results in more object allocation and more memory usage. I consider this performance difference as a pathological case, and would not expect it to be a concern in practice.

#### Standard Library polymorphism

The standard library URIs are implemented with different classes and `URI(uri)` returns types determined by the URI *scheme* (https, ftp, etc.). Addressable only offers a single `Addressable::URI` class, and `.parse` will always return the same type of object. This presents trade-offs: *scheme*-specific methods decorating a `URI` may be useful, but using them could introduce runtime errors when an unexpected type is parsed by `URI.parse(uri)`. It also can result in some nonsense when you change the *scheme* and the object remains a type representing a different *scheme*. Take this example:
```ruby
standard_uri = URI("https://www.rfc-editor.org/rfc/rfc3986.txt") # => => #<URI::HTTPS https://www.rfc-editor.org/rfc/rfc3986.txt>
standard_uri.scheme = "ftp"
standard_uri # => #<URI::HTTPS ftp://www.rfc-editor.org/rfc/rfc3986.txt>
standard_uri.typecode
# NoMethodError: undefined method `typecode' for #<URI::HTTPS ftp://www.rfc-editor.org/rfc/rfc3986.txt>

URI(standard_uri.to_s) # => #<URI::FTP ftp://www.rfc-editor.org/rfc/rfc3986.txt>
URI(standard_uri.to_s).typecode # => nil

standard_scheme_typo_uri = URI("htps://www.rfc-editor.org/rfc/rfc3986.txt") # => #<URI::Generic htps://www.rfc-editor.org/rfc/rfc3986.txt>
standard_scheme_typo_uri.request_uri
# NoMethodError: undefined method `request_uri' for #<URI::Generic htps://www.rfc-editor.org/rfc/rfc3986.txt>
```

This sort of runtime error is not possible in `Addressable` since there is a single, predictable type of object to expect from the `parse` method.

```ruby
# uri_tests.rb

standard_uri2 = URI(standard_uri)
standard_uri2.scheme = "ftp"
standard_uri # => #<URI::HTTPS ftp://www.rfc-editor.org/rfc/rfc3986.txt>

addressable_uri2 = Addressable::URI.parse(addressable_uri)
addressable_uri2.scheme = "ftp"
addressable_uri # => #<Addressable::URI:0x424 URI:https://www.rfc-editor.org/rfc/rfc3986.txt>


# => #<URI::HTTPS https://www.rfc-editor.org/rfc/rfc3986.txt>
```

### URI Templates

The true power feature of the `Addressable` gem is URI templates.
I leave you with examples from the README to explore further:


```ruby

require "addressable/template"

template = Addressable::Template.new("http://example.com/{?query*}")
template.expand({
  "query" => {
    'foo' => 'bar',
    'color' => 'red'
  }
})
#=> #<Addressable::URI:0xc9d95c URI:http://example.com/?foo=bar&color=red>

template = Addressable::Template.new("http://example.com/{?one,two,three}")
template.partial_expand({"one" => "1", "three" => 3}).pattern
#=> "http://example.com/?one=1{&two}&three=3"

template = Addressable::Template.new(
  "http://{host}{/segments*}/{?one,two,bogus}{#fragment}"
)
uri = Addressable::URI.parse(
  "http://example.com/a/b/c/?one=1&two=2#foo"
)
template.extract(uri)
#=>
# {
#   "host" => "example.com",
#   "segments" => ["a", "b", "c"],
#   "one" => "1",
#   "two" => "2",
#   "fragment" => "foo"
# }
```


## Find Out More

### References

* Home  :: [github.com/sporkmonger/addressable](https://github.com/sporkmonger/addressable)
* Gem   :: [addressable](https://rubygems.org/gems/addressable)
