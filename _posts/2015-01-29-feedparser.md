---
layout: gem
title:  "Week #5 - feedparser gem - web feed parser and normalizers (for RSS 2.0, Atom, n friends)"
---

## What's a Web Feed?

A web feed (or news feed) is a simple document/data format
that 1) lets you publish a list of
status updates, blog postings, articles, pictures, cartoons, recordings, etc. and 
that 2) lets others subscribe to your updates.

Example:

~~~
<feed>
  <title>beer.db - Open Beer, Brewery n Brewpubs Data News and Updates</title>
  <link href="http://openbeer.github.io/" />
  <updated>2015-01-04T09:25:14+00:00</updated>
  <entry>
    <title>Beer-A-Day - Open Data Eternal Page-A-Day Calender</title>
    <link href="http://openbeer.github.io/2014/12/12/beer-a-day.html" />
    <updated>2014-12-12T00:00:00+00:00</updated>
    <summary>As an example of using the beer.db...</summary>
  </entry>
  <entry>
    <title>New beer.db Build System - Welcome ./Datafile e.g. $beerdb new at</title>
    <link href="http://openbeer.github.io/2014/12/01/new-build-system-w-datafile.html" />
    <updated>2014-12-01T00:00:00+00:00</updated>
    <summary>The beerdb command line tool now includes a new build system...</summary>
  ...
</feed>
~~~

Ruby ships with a standard library that lets you read web feeds in the "classic"
Really Simple Syndication (RSS 1.0/2.0) flavors
or in the "modern" Atom Publishing format.

### Task I: Reading Web Feeds in the Really Simple Syndication (RSS) Format

Let's read in a "classic" web feed in the Really Simple Syndication (RSS)
format from the RubyFlow site and print out the latest headlines:

~~~
require 'rss'
require 'open-uri'

feed  = RSS::Parser.parse( 'http://www.rubyflow.com/rss' )

puts "==  #{feed.channel.title} =="

feed.items.each do |item|
  puts "- #{item.title}"
  puts "  (#{item.link})"
  puts
end
~~~

Prints:

~~~
==  RubyFlow ==

- The All New RubyFlow for 2015
  (http://www.rubyflow.com/p/8hqxpd-the-all-new-rubyflow-for-2015)

- Jekyll Quick Reference (Cheat Sheet) for Static Site Generator
  (http://www.rubyflow.com/p/k8l2im-jekyll-quick-reference-cheat-sheet-for-static-site-generator)

- Timeago date filter for Liquid templates
  (http://www.rubyflow.com/p/12092-timeago-date-filter-for-liquid-templates)
~~~

To include a post's summary use `item.description` or to include a post's full-text
use `content_encoded`. Example:

~~~
puts item.description
puts item.content_encoded
~~~

That's it.

### Task II: Reading Web Feeds in the Atom Publishing Format

Next let's read a "modern" web feed in the Atom Publishing format
from the beer.db - Open Beer Data site and print out the latest headlines:

~~~
feed = RSS::Parser.parse( 'http://openbeer.github.io/atom.xml' )

puts "== #{feed.title.content} =="

feed.entries.each do |entry|
  puts "- #{entry.title.content}"
  puts "  (#{entry.link.href})"
  puts
end
~~~

And use `entry.content.content` to get a post's full-text. That's it.

Note, that the standard library maps the different web feed flavors 1:1 to Ruby fields
reflecting the different formats e.g. the feed title stored in `feed.channel.title` in RSS 2.0
becomes `feed.title.content` in Atom 
and the feed item's full-text stored in `feed.item.content_encoded` in RSS 2.0
becomes `feed.entry.content.content` in Atom (Yes, it's `content.content`).
Welcome to the wonderful world of web feed formats. 


## What's the `feedparser` gem?

The `feedparser` gem lets you read in web feeds in the RSS 2.0
or Atom Publishing formats (using the built-in libraries)
and normalizes the feed and item fields e.g. `item.content` gets mapped to
`item.content_encoded` in RSS 2.0 and to `entry.content.content` in Atom and so on.

### `Feed` Struct

#### Mappings

**Title 'n' Summary**

| Feed Struct        | RSS 2.0           | Notes               | Atom          | Notes               |
| ------------------ | ----------------- | ------------------- | ------------- | ------------------- |
| `feed.title`       | `title`           | plain vanilla text  | `title`       | plain vanilla text  |
| `feed.summary`     | `description`     | plain vanilla text  | `subtitle`?   | plain vanilla text  |

Note: The Feed parser will remove all HTML tags and attributes from the title (RSS 2.0+Atom), 
description (RSS 2.0) and subtitle (Atom) content and will unescape HTML entities e.g. `&amp;`
becomes & and so on - always resulting in plain vanilla text.

**Dates**

| Feed Struct        | RSS 2.0             | Notes             | Atom       | Notes           |
| ------------------ | ------------------- | ----------------- | ---------- | --------------- |
| `feed.updated`     | `lastBuildDate`?    | RFC-822 format    | `updated`  | ISO 801 format  |
| `feed.published`   | `pubDate`?          | RFC-822 format    |  -         |                 |


RFC-822 date format e.g. Wed, 14 Jan 2015 19:48:57 +0100

ISO-801 date format e.g. 2015-01-14T19:48:57Z

Note: Mappings use question mark (`?`) for optional elements (otherwise assume required elements).

~~~
class Feed
  attr_accessor :format   # e.g. atom|rss 2.0|etc.
  attr_accessor :title    # note: always plain vanilla text
  attr_accessor :url

  attr_accessor :items

  attr_accessor :summary   # note: is description in RSS 2.0 and subtitle in Atom; always plain vanilla text

  attr_accessor :updated     # note: is lastBuildDate in RSS 2.0
  attr_accessor :published   # note: is pubDate in RSS 2.0; not available in Atom

  attr_accessor :generator
  attr_accessor :generator_version  # e.g. @version (atom)
  attr_accessor :generator_uri      # e.g. @uri     (atom)
end
~~~


### `Item` Struct

**Title 'n' Summary**

| Feed Struct        | RSS 2.0           | Notes               | Atom          | Notes               |
| ------------------ | ----------------- | ------------------- | ------------- | ------------------- |
| `item.title`       | `title`           | plain vanilla text  | `title`       | plain vanilla text  |
| `item.summary`     | `description`     | plain vanilla text  | `summary`?    | plain vanilla text  |
| `item.content`     | `content`?        | html                | `content`?    | html                |

Note: The Feed parser will remove all HTML tags and attributes from the title (RSS 2.0+Atom), 
description (RSS 2.0) and summary (Atom) content
and will unescape HTML entities e.g. `&amp;` becomes & and so on - always
resulting in plain vanilla text.

Note: In plain vanilla RSS 2.0 there's no difference between (full) content and summary - everything is wrapped
in a description element; however, best practice is using the content "module" from RSS 1.0 inside RSS 2.0.
If there's no content module present the feed parser will "clone" the description
and use one version for `item.summary` and the clone for `item.content`.

**Dates**

| Item Struct        | RSS 2.0             | Notes             | Atom          | Notes           |
| ------------------ | ------------------- | ----------------- | ------------- | --------------- |
| `item.updated`     | `pubDate`?          | RFC-822 format    | `updated`     | ISO 801 format  |
| `item.published`   | -                   | RFC-822 format    | `published`?  | ISO 801 format  |

Note: In plain vanilla RSS 2.0 there's only one `pubDate` for items,
thus, it's not possible to differentiate between published and updated dates for items;
note - the `item.pubDate` will get mapped to `item.updated`. To set the published date in RSS 2.0
use the dublin core module e.g `dc:created`, for example.

~~~
class Item
  attr_accessor :title   # note: always plain vanilla text
  attr_accessor :url

  attr_accessor :content
  attr_accessor :content_type  # optional for now (text|html|etc.)

  attr_accessor :summary

  attr_accessor :updated    # note: is pubDate in RSS 2.0 and updated in Atom
  attr_accessor :published  # note: is published in Atom; not available in RSS 2.0 (use dc:created)

  attr_accessor :guid
end
~~~


### Reading Web Feeds in All Formats

~~~
require 'open-uri'
require 'feedparser'

url = 'http://openbeer.github.io/atom.xml'
xml = open( url ).read

feed = FeedParser::Parser.parse( xml )
puts "== #{feed.title} =="

feed.items.each do |item|
  puts "- #{item.content}"
  puts "  (#{item.url})"
  puts
end
~~~

Prints:

~~~
== beer.db - Open Beer, Brewery n Brewpubs Data News and Updates ==

- Beer-A-Day - Open Data Eternal Page-A-Day Calender
  (http://openbeer.github.io/2014/12/12/beer-a-day.html)

- New beer.db Build System - Welcome ./Datafile e.g. $beerdb new be
  (http://openbeer.github.io/2014/12/01/new-build-system-w-datafile.html)
 
- New Repo /maps - Free Full-Screen Interactive Beer Maps w/ Brewery Listings
  (http://openbeer.github.io/2014/11/11/new-repo-maps.html)
~~~

Now change the feed url to:

~~~
url = 'http://www.rubyflow.com/rss'
~~~

and everything will work without changes for both formats (that is, RSS and Atom). That's it.

## Bonus: Planet Feed Reader in 20 Lines of Ruby

`planet.rb`:

~~~
require 'open-uri'
require 'feedparser'
require 'erb'
  
# step 1) read a list of web feeds

FEED_URLS = [
  'http://vienna-rb.at/atom.xml',
  'http://rubyflow.com/rss',
  'http://openfootball.github.io/atom.xml',
  'http://openbeer.github.io/atom.xml'
]

items = []

FEED_URLS.each do |url|
  feed = FeedParser::Parser.parse( open( url ).read )
  items += feed.items
end

# step 2) mix up all postings in a new page

FEED_ITEM_TEMPLATE = <<EOS
<% items.each do |item| %>
  <div class="item">
    <h2><a href="<%= item.url %>"><%= item.title %></a></h2>
    <div><%= item.content %></div>
  </div>
<% end %>
EOS

puts ERB.new( FEED_ITEM_TEMPLATE ).result
~~~

Run the script:

~~~
$ ruby planet.rb      
~~~

Prints:

~~~
<div class="item">
  <h2><a href="http://vienna-rb.at/blog/2015/01/28/picks/">Picks / what the vienna.rb team thinks is worth sharing this week</a></h2>
  <div>
   <h3>01/28 Picks!</h3>
   <p>In a series on this website we'll entertain YOU with our picks...
 ...
~~~

## Find Out More 

* home  :: [github.com/feedreader/feed.parser](https://github.com/feedreader/feed.parser)
* gem   :: [rubygems.org/gems/feedparser](https://rubygems.org/gems/feedparser)
* rdoc  :: [rubydoc.info/gems/feedparser](http://rubydoc.info/gems/feedparser)
