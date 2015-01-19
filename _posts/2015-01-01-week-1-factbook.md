---
layout: post
title:  "Week #1 - factbook gem - turn the world factbook into open structured data e.g JSON"
---

## What's the World Factbook?

The World Factbook [1][2] published by the Central Intelligence Agency (CIA) 
offers free country profiles in the public domain (that is, no copyright(s), no rights reserved).

- [1] [The World Factbook](https://www.cia.gov/library/publications/the-world-factbook/)
- [2] [Wikipedia Article: The World Factbook](http://en.wikipedia.org/wiki/The_World_Factbook)

## What's the factbook gem?

The factbook gem ships with scripts for the world factbook that let you get open structured data 
e.g JSON and friends. Example:

To get country profile page for Brazil as a hash (that is, structured data e.g. nested key/values)
use:

~~~
page = Factbook::Page.new( 'br' )   # br is the country code for Brazil
pp page.data                        # pretty print hash
~~~

To save the hash to disk as JSON use:

~~~
page = Factbook::Page.new( 'br' )
File.open( 'br.json', 'w') do |f|
  f.write page.to_json( pretty: true )
end
~~~

That's it.

## Bonus: Ready-To-Use Public Domain Factbook Datasets

[openmundi/factbook.json](https://github.com/openmundi/factbook.json) - open (public domain)
factbook country profiles in JSON for all the world's countries (using internet domain names
for country codes e.g. Austria is `at.json` not `au.json`, 
Germany is `de.json` not `gm.json` and so on)


## Find Out More 

* home  :: [github.com/worlddb/factbook.ruby](https://github.com/worlddb/factbook.ruby)
* gem   :: [rubygems.org/gems/factbook](https://rubygems.org/gems/factbook)
* rdoc  :: [rubydoc.info/gems/factbook](http://rubydoc.info/gems/factbook)
