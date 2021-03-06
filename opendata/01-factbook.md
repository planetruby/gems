# Day 1 - factbook Gem - Turn the World Factbook into Open Structured Data (e.g JSON) - Free Intelligence on the History, People, Government, Economy, Energy, Geography, Communications, Transportation, Military 'n' Terrorism for 260+ Countries


Written by {% avatar geraldb %} [Gerald Bauer](https://github.com/geraldb)

_A code monkey and enthusiastic collector (and publisher) of open football and beer data. Skills include Ruby, SQLite and CSV. Spec lead of [CSV <3 JSON](https://github.com/csvspecs/csv-json)._



Let's start with a trivia quiz:

Q: What country has the biggest
stock of proved reserves¹ of crude oil in barrels (bbl) - according to the Central Intelligence Agency (CIA)?

- (A) Canada
- (B) Iraq
- (C) Iran
- (D) Saudi Arabia
- (E) Venezuela
- (F) Other, please tell!

¹: Proved reserves are those quantities of petroleum which, by analysis of geological and engineering data, can be estimated with a high degree of confidence to be commercially recoverable from a given date forward, from known reservoirs and under current economic conditions.





## What's the World Factbook?

The World Factbook [1][2] published by the United States' Central Intelligence Agency (CIA)
offers free country profiles in the public domain, that is, no copyright(s), no rights reserved.

- [1] [The World Factbook](https://www.cia.gov/the-world-factbook/)
- [2] [Wikipedia Article: The World Factbook](http://en.wikipedia.org/wiki/The_World_Factbook)



## What's the factbook gem?

The factbook library ships with scripts for the world factbook that let you turn country profiles into open structured data
e.g JSON and friends. Example:


To get the country profile page for Brazil as a hash (that is, structured data e.g. nested key/values)
use:

``` ruby
page = Factbook::Page.new( 'br' )   # br is the country code for Brazil
pp page.to_h                        # pretty print hash
```

resulting in:

``` ruby
{"Introduction"=>
  {"Background"=>
    {"text"=>
      "Following more than three centuries under Portuguese rule,
       Brazil gained its independence in 1822, ..."}},
 "Geography"=>
  {"Location"=>{"text"=>"Eastern South America, bordering the Atlantic Ocean"},
   "Geographic coordinates"=>{"text"=>"10 00 S, 55 00 W"},
   "Map references"=>{"text"=>"South America"},
   "Area"=>
    {"total"=>{"text"=>"8,515,770 sq km"},
     "land"=>{"text"=>"8,358,140 sq km"},
     "water"=>{"text"=>"157,630 sq km"},
     "note"=>
      {"text"=>
        "includes Arquipelago de Fernando de Noronha, Atol das Rocas, ..."}},
   "Area - comparative"=>
    {"text"=>"slightly smaller than the US"},
   "Land boundaries"=>
    {"total"=>{"text"=>"16,145 km"},
     "border countries (10)"=>
      {"text"=>
        "Argentina 1,263 km, Bolivia 3,403 km, Colombia 1,790 km,
        French Guiana 649 km, Guyana 1,308 km, Paraguay 1,371 km, Peru 2,659 km,
        Suriname 515 km, Uruguay 1,050 km, Venezuela 2,137 km"}},
   "Climate"=>{"text"=>"mostly tropical, but temperate in south"},
   "Elevation extremes"=>
    {"lowest point"=>{"text"=>"Atlantic Ocean 0 m"},
     "highest point"=>{"text"=>"Pico da Neblina 2,994 m"}},
   "Natural resources"=>
    {"text"=>
      "bauxite, gold, iron ore, manganese, nickel, phosphates, ..."},
    ...
```

### Use data attributes

``` ruby
pp page['Introduction']['Background']['text']
# => "Following more than three centuries..."
pp page['Geography']['Area']['total']['text']
# => "8,515,770 sq km"
pp page['Geography']['Area']['land']['text']
# => "8,358,140 sq km"
pp page['Geography']['Area']['water']['text']
# => "157,630 sq km"
pp page['Geography']['Area']['note']
# => "includes Arquipelago de Fernando de Noronha, Atol das Rocas, ..."
pp page['Geography']['Area - comparative']['text']
# => "slightly smaller than the US"
pp page['Geography']['Climate']['text']
# => "mostly tropical, but temperate in south"
pp page['Geography']['Terrain']['text']
# => "mostly flat to rolling lowlands in north; ..."
pp page['Geography']['Elevation']['lowest point']['text']
# => "Atlantic Ocean 0 m"
pp page['Geography']['Elevation']['highest point']['text']
# => "Pico da Neblina 2,994 m"
pp page['Geography']['Natural resources']['text']
# => "bauxite, gold, iron ore, manganese, nickel, phosphates, ..."
...
```

See [Attributes](https://github.com/factbook/factbook/blob/master/ATTRIBUTES.md) for a quick reference listing.



### Save to disk as JSON

``` ruby
page = Factbook::Page.new( 'br' )
File.open( 'br.json', 'w:utf-8') do |f|
  f.write page.to_json
end
```


### List all codes

``` ruby
Factbook.codes.each do |code|
  pp code
end
```

resulting in:

```
#<Factbook::Codes::Code
 code="af",
 name="Afghanistan",
 category="Countries",
 region="South Asia">
#<Factbook::Codes::Code
 code="al",
 name="Albania",
 category="Countries",
 region="Europe">
#<Factbook::Codes::Code
 code="ag",
 name="Algeria",
 category="Countries",
 region="Africa">
#<Factbook::Codes::Code
 code="an",
 name="Andorra",
 category="Countries",
 region="Europe">
...
```

Note: You can filter codes by category e.g. Countries, Dependencies, Miscellaneous, Oceans, etc.
and/or by region e.g. Africa, Europe, South Asia, Central America and Caribbean, etc.


``` ruby

assert_equal 260, Factbook.codes.size

## categories
assert_equal 195, Factbook.codes.countries.size
assert_equal  52, Factbook.codes.dependencies.size
assert_equal   5, Factbook.codes.oceans.size
assert_equal   1, Factbook.codes.world.size
assert_equal   2, Factbook.codes.others.size
assert_equal   5, Factbook.codes.misc.size

## regions
assert_equal  55, Factbook.codes.europe.size
assert_equal   9, Factbook.codes.south_asia.size
assert_equal   6, Factbook.codes.central_asia.size
assert_equal  22, Factbook.codes.east_n_souteast_asia.size
assert_equal  19, Factbook.codes.middle_east.size
assert_equal  55, Factbook.codes.africa.size
assert_equal   7, Factbook.codes.north_america.size
assert_equal  33, Factbook.codes.central_america_n_caribbean.size
assert_equal  14, Factbook.codes.south_america.size
assert_equal  30, Factbook.codes.australia_oceania.size
assert_equal   4, Factbook.codes.antartica.size
assert_equal   5, Factbook.codes.region('Oceans').size
assert_equal   1, Factbook.codes.region('World').size

## categories + regions
assert_equal  45, Factbook.codes.countries.europe.size
...
```

See [`data/codes.csv`](https://github.com/factbook/factbook/blob/master/factbook-codes/data/codes.csv) for the built-in listing of all codes with categories and regions.


Note: The World Factbook includes 266 entries - 195 sovereign countries, 2 others, 58 dependencies, 5 miscellaneous,
5 oceans, and 1 world profile.

That's it. Explore the world.



## Bonus: Ready-To-Use Public Domain Factbook Datasets

[factbook/factbook.json](https://github.com/factbook/factbook.json) - open (public domain)
factbook country profiles in JSON for all the world's countries.
Note: All country profiles use the "official" two-letter GEC (formerly FIPS) codes
and NOT the ISO codes (you might be used to) e.g. au for Austria (and not at), gm for Germany (and not de), and so on.


## Find out more

### References

- Home :: [github.com/factbook/factbook](https://github.com/factbook/factbook)
- Gem :: [factbook](https://rubygems.org/gems/factbook)
- Docs :: [factbook](http://rubydoc.info/gems/factbook)
- Talk Notes :: [Turn the World Factbook into Open (Structured) Data.](https://github.com/geraldb/talks/blob/master/factbook.md)

