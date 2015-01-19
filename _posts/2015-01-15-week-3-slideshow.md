---
layout: post
title:  "Week #3 - slideshow gem - a free web alternative to PowerPoint and Keynote in Ruby"
---


## What's Slide Show (S9)?

A Ruby gem that lets you create slide shows and author slides in plain text
using a wiki-style markup language that's easy-to-write and easy-to-read.
The Slide Show (S9) project also collects and welcomes themes
and ships "out-of-the-gem" with built-in support for "loss-free" gradient vector graphics themes.

## Getting Started in 1-2-3 Easy Steps

* Step 1: Author your slides in plain text using a wiki-style markup language
* Step 2: Build your slide show using the `slideshow` gem
* Step 3: Open up your slide show (web page) in your browser and hit the space bar to flip through your slides
* That's it. Showtime!

### Step 1: Author your slides in plain text using a wiki-style markup language

Slide Show lets you author your slides using a wiki-style markup language
that's easy-to-write and easy-to-read. Let's create some slides about best practices for web services
using REST:

~~~
# Web Services REST-Style: Universal Identifiers, Formats & Protocols

Agenda

- What's REST?
- Universal Identifiers, Formats & Protocols
- The Holy REST Trinity - Noun, Verbs, Types
- REST Design Principles 
- Architecture Astronaut REST Speak


# Representational State Transfer (REST) - Meaningless Acronym? Wordplay?

rest - n. - peace, ease, or refreshment resulting from the insight that the web works

No matter what vendors tell you - no need to "Light Up the Web" - relax - built on
an *open architecture using universal identifiers, formats & protocols and __evolving__
open standards* - no need to reinvent the wheel and sign-up for single-vendor offerings.

### Broad Definition

- Best Practices for Designing Web Services for a Unified Human and Programable Web

### Narrow Definition

- Alternative to BigCo Web Services (SOAP, WS-*) and RPC-Style Web Services (XML-RPC)
~~~

Use `#` headings to start a new slide in Markdown. That's it.


### Step 2: Build your slide show using the `slideshow` gem

Run `slideshow` to build your slide show. The `slideshow` gem
expects the name of your slide show source document (e.g. `rest`)
without the `.markdown` or `.textile` ending and will build a web page
(e.g. [`rest.html`](http://slideshow-s9.github.io/demos/rest.html)
that is an all-in-one-page handout and a live slide show all at once.

~~~
$ slideshow build rest

=> Preparing slideshow 'rest.html'...
=> Done.
~~~

### Step 3: Open up your slide show in your browser

Open up your slide show [`rest.html`](http://slideshow-s9.github.io/demos/rest.html)
in your browser (Firefox, Chrome, Opera, Safari, and others) and hit F11 to switch 
into full screen projection and hit the space bar or the right arrow, down arrow
or page down key to flip through your slides.


That's it. Voila.

### Bonus: Try some different templates/theme packs

* [S6 PDF Theme](http://slideshow-s9.github.io/demos/tutorial.pdf.html) -> [PDF](http://slideshow-s9.github.io/demos/tutorial.pdf)
* [Google HTML5 Slides Theme](http://slideshow-s9.github.io/demos/packs/g5/tutorial1.html5.html)
* [S5 Blank](http://slideshow-s9.github.io/demos/packs/s5/tutorial1.html)
* [Slidy W3C Blue Theme](http://slideshow-s9.github.io/demos/packs/slidy/tutorial1.w3c.html)
* [More »](http://slideshow-s9.github.io/gallery.html)


## Find Out More 

* home  :: [github.com/slideshow-s9/slideshow](https://github.com/slideshow-s9/slideshow)
* gem   :: [rubygems.org/gems/slideshow](https://rubygems.org/gems/slideshow)
* rdoc  :: [rubydoc.info/gems/slideshow](http://rubydoc.info/gems/slideshow)

