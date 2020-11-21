# Week 1 - factbook gem - turn the world factbook into open structured data e.g JSON


## What's the World Factbook?

The World Factbook [1][2] published by the Central Intelligence Agency (CIA)
offers free country profiles in the public domain (that is, no copyright(s), no rights reserved).

- [1] [The World Factbook](https://www.cia.gov/library/publications/the-world-factbook/)
- [2] [Wikipedia Article: The World Factbook](http://en.wikipedia.org/wiki/The_World_Factbook)

## What's the factbook gem?

The factbook gem ships with scripts for the world factbook that let you turn web pages into open structured data
e.g JSON and friends. Example:

To get the country profile page for Austria as a hash (that is, structured data e.g. nested key/values)
use:

~~~
page = Factbook::Page.new( 'at' )   # at is the country code for Austria
pp page.data                        # pretty print hash
~~~

To save the hash to disk as JSON use:

~~~
page = Factbook::Page.new( 'at' )
File.open( 'at.json', 'w') do |f|
  f.write page.to_json( pretty: true )
end
~~~

Resulting in:

~~~
{
  "intro": {
    "background": {
      "text": "Once the center of power for the large Austro-Hungarian Empire,
               Austria was reduced to a small republic after its defeat in World War ..."
    }
  },
  "geo": {
    "location": {
      "text": "Central Europe, north of Italy and Slovenia"
    },
    "geographic_coordinates": {
      "text": "47 20 N, 13 20 E"
    },
    "map_references": {
      "text": "Europe"
    },
    "area": {
      "total": "83,871 sq km",
      "land": "82,445 sq km",
      "water": "1,426 sq km"
    },
    "area_comparative": {
      "text": "slightly smaller than Maine"
    },
    "land_boundaries": {
      "total": "2,562 km",
      "border_countries": "Czech Republic 362 km, Germany 784 km, Hungary 366 km, Italy 430 km, Liechtenstein 35 km, Slovakia 91 km, Slovenia 330 km, Switzerland 164 km"
    },
    "coastline": {
      "text": "0 km (landlocked)"
    },
    "maritime_claims": {
      "text": "none (landlocked)"
    },
    "climate": {
      "text": "temperate; continental, cloudy; cold winters with frequent rain and some snow in lowlands and snow in mountains; moderate summers with occasional showers"
    },
    "terrain": {
      "text": "in the west and south mostly mountains (Alps); along the eastern and northern margins mostly flat or gently sloping"
    },
    "elevation_extremes": {
      "lowest_point": "Neusiedler See 115 m",
      "highest_point": "Grossglockner 3,798 m"
    },
    "natural_resources": {
      "text": "oil, coal, lignite, timber, iron ore, copper, zinc, antimony, magnesite, tungsten, graphite, salt, hydropower"
    },
    ...
~~~

Note: The World Factbook includes 267 entries - 195 sovereign countries, 2 others, 58 dependencies, 6 miscellaneous,
5 oceans, and 1 world profile.

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
