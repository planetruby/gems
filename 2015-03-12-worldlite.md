---
layout: gem
title:  "Week #11 - worldlite gem  - lightweight public domain country data (all data included as good ol' ruby code)"
---

Ever wondered how many countries are in the world? 222? 204? 196? 193?
It all depends - of course - what's your definition of country is.
The United Nations lists 193 countries (as members), the World Football Association (that is, FIFA)
lists 209 countries (as members) plus 13 more countries (as members of continental federations only),
or the Olympics Committee lists 204 countries or, the ISO 3166 country code standard
lists 196 countries, for example, and so on.
Now lets say you need a country list for all FIFA members for the World Cup Qualifiers 2018 in Russia in Ruby?


## What's the worldlite gem?

Let's welcome the worldlite gem that includes "lightweight" public domain country data -
that is, all data gets included as good ol' ruby code - no database, no dependencies, no copyright.
For example, the class defition for Austria reads:

~~~
module WorldLite

  AT = Country.new do |c|

    c.name   = 'Austria'
    c.key    = 'at'
    c.alpah2 = 'AT'   # iso two-letter country code (ISO 3166-1 alpha-2)
    c.alpha3 = 'AUT'  # iso three-letter country code (ISO 3166-1 alpha-3)
    c.fifa   = 'AUT'  # football country code (fifa = Fédération Int'le de Football Association)
    c.ioc    = 'AUT'  # olympics country code (ioc = International Olympic Committee)
    c.net    = 'at'   # internet top level domain
    c.motor  = 'A'    # motor vehicle license plate code
    c.num3   = '040'  # iso numeric three-digits code as string (ISO 3166-1 numeric)
    c.num    = 40     # iso numeric code as number

    c.continent_name =  'Europe'

    c.un     = true     # United Nations member?  -- 193 member countries
    c.eu     = true     # European Union member?  -- 27 member countries
    c.euro   = true     # Euro Zone member?       -- 17 member countries

    c.wikipedia = 'Austria'   # e.g. see en.wikipedia.org/wiki/Austria
    c.wikidata  = 40          # e.g. see wikidata.org/wiki/Q40
    c.factbook  = 'au'        # e.g. see www.cia.gov/.../the-world-factbook/geos/au.html
    
  end

  WORLD      << AT
  WORLD_UN   << AT
  WORLD_ISO  << AT
  WORLD_FIFA << AT

  EUROPE       << AT
  EUROPE_EU    << AT
  EUROPE_EURO  << AT

end  # module WorldLite
~~~


And you can use it in Ruby like:

~~~
require 'worldlite'

include WorldLite

WORLD.size       # => 245
WORLD_UN.size    # => 193
WORLD_G20.size   # => 20
WORLD_FIFA.size  # => 243
WORLD_WTO.size   # => 157
WORLD_OECD.size  # => 34

EUROPE.size      # => 51
EUROPE_UEFA.size # => 54
EUROPE_EU.size   # => 27
EUROPE_EURO.size # => 17

AT.class.name     # => 'WorldLite::Country'
AT.name           # => 'Austria'
AT.continent_name # => 'Europe'
AT.alpha3         # => 'AUT'
AT.slug           # => 'austria'
AT.un?            # => true
AT.fifa?          # => true
AT.g20?           # => false
AT.eu?            # => true
AT.euro?          # => true

AT.wikpedia       # => 'Austria'
AT.wikidata       # => 40
AT.wikpedia_url   # => 'http://en.wikipedia.org/wiki/Austria'
AT.wikidata_url   # => 'http://www.wikidata.org/wiki/Q40'
AT.factbook       # => 'au'
AT.factbook_url   # => 'http://www.cia.gov/.../the-world-factbook/geos/au.html'
~~~

That's it.


## Bonus Question 

Now you might wonder - where's the country data coming from and who's going to keep it up-to-date?

The answer is recursive.  First, the good ol' ruby code gets auto-generated with a ruby script
using the world.db. The country template, for example, uses embedded Ruby (erb)
and - surprise, surprise - looks like:

~~~
module WorldLite

  c = Country.new
  c.name   = <%= fmt_str( country.name ) %>
  c.key    = <%= fmt_str( country.key )  %>
  c.alpha3 = <%= fmt_str( country.iso3 ) %>
  c.fifa   = <%= fmt_str( country.fifa ) %>
  c.net    = <%= fmt_str( country.net  ) %>
  
  c.continent_name =  <%= fmt_str( country.continent.name ) %>

  c.un     = <%= fmt_bool( has_tag?( country, 'un' )) %>
  c.eu     = <%= fmt_bool( has_tag?( country, 'eu' )) %>
  c.euro   = <%= fmt_bool( has_tag?( country, 'euro' )) %>
  
  WORLD      << <%= country.key.upcase %>

<% if has_tag?( country, 'un' ) %>
  WORLD_UN   << <%= country.key.upcase %>
<% end %>
<% if country.iso3 %>
  WORLD_ISO  << <%= country.key.upcase %>
<% end %>
<% if country.fifa %>
  WORLD_FIFA << <%= country.key.upcase %>
<% end %>
...
~~~

The world.db itself gets build from plain text datasets that get hosted on GitHub and, thus, 
updating the world.db datasets works like updating anything on GitHub. Update it in your brower like-a-wiki
or use a pull request and so on.


## Find Out More

worldlite

* home     :: [github.com/worlddb/world.lite.ruby](https://github.com/worlddb/world.lite.ruby)
* gem      :: [rubygems.org/gems/worldlite](https://rubygems.org/gems/worldlite)
* rdoc     :: [rubydoc.info/gems/worldlite](http://rubydoc.info/gems/worldlite)

world.db (datasets)

* home     :: [github.com/openmundi](https://github.com/openmundi)
