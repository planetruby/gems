# Day 1 - fifa Gem - The World's Football Countries and Codes (Incl. Non-Member Countries and Irregular Codes)


Written by {% avatar geraldb %} [Gerald Bauer](https://github.com/geraldb)


Let's start with a triva quiz.
The United Nations has 193 member countries:

Q: How many member countries has the world football federation (FIFA)?

- (A) 193
- (B) 211
- (C) 312
- (D) Other, please tell.




## What's the fifa Gem?

The fifa gem ships with all the world's countries (incl.
non-members or historic) and the countries' official (or irregular) three-letter codes.  Example:


Get and pretty print (pp) all countries

``` ruby
require 'fifa'

pp Fifa.countries
```

resulting in:

```
[#<Country @code="AFG", @key="af", @name="Afghanistan">,
 #<Country @code="ALB", @key="al", @name="Albania">,
 #<Country @code="ALG", @key="dz", @name="Algeria">,
 #<Country @code="ASA", @key="as", @name="American Samoa">,
 #<Country @code="AND", @key="ad", @name="Andorra">,
 #<Country @code="ANG", @key="ao", @name="Angola">,
 #<Country @code="AIA", @key="ai", @name="Anguilla">,
 #<Country @code="ATG", @key="ag", @name="Antigua and Barbuda">,
 #<Country @code="ARG", @key="ar", @name="Argentina">,
 #<Country @code="ARM", @key="am", @name="Armenia">,
 #<Country @code="ARU", @key="aw", @name="Aruba">,
 #<Country @code="AUS", @key="au", @name="Australia">,
 #<Country @code="AUT", @key="at", @name="Austria">,
 #<Country @code="AZE", @key="az", @name="Azerbaijan">,
 ...
]
```

Note: If you prefer you can use the all-upcase `FIFA` name as an alias for `Fifa`.


Let's lookup some countries by the fifa (three-letter) code:

``` ruby
eng = Fifa['ENG']
# -or-
eng = Fifa['eng']
eng = Fifa[:eng]

eng.key    #=> "eng"
eng.name   #=> "England"
eng.code   #=> "ENG"


aut  = Fifa['AUT']
# -or-
aut  = Fifa['aut']
aut  = Fifa[:aut]

aut.key    #=> "at"
aut.name   #=> "Austria"
aut.code   #=> "AUT"

...
```

Or get all countries / members by continental confederation:

- Asian Football Confederation (AFC; 47 members) (a)
- Confederation of African Football (CAF; 56 members)
- Confederation of North, Central American and Caribbean Association Football (CONCACAF; 41 members) (b)
- Confederación Sudamericana de Fútbol (CONMEBOL; 10 members)
- Oceania Football Confederation (OFC; 14 members) (a)
- Union of European Football Associations (UEFA; 55 members) (c)

(a) Australia has been a member of the AFC since 2006.

(b) French Guiana, Guyana and Suriname are CONCACAF members although they are in South America. The French Guiana team is a member of CONCACAF but not of FIFA.

(c) Teams representing the nations of Armenia, Azerbaijan, Cyprus, Georgia, Israel, Kazakhstan, Russia and Turkey are UEFA members, although the majority or entirety of their territory is outside of continental Europe. Monaco is not member of UEFA or FIFA.


``` ruby
Fifa.members( 'FIFA' ).size          #=> 211 members  -or-
Fifa.members( 'World' ).size         #=> 211 members

Fifa.members( 'AFC' ).size           #=> 47 members   -or-
Fifa.members( 'Asia' ).size          #=> 47 members

Fifa.members( 'CAF').size            #=> 56 members  -or-
Fifa.members( 'Africa' ).size        #=> 56 members

Fifa.members( 'CONCACAF').size       #=> 41 members  -or-
Fifa.members( 'North, Central America and Caribbean').size   #=> 41 members

Fifa.members( 'CONMEBOL' ).size      #=> 10 members  -or-
Fifa.members( 'South America' ).size #=> 10 members

Fifa.members( 'OFC' ).size           #=> 14 members  -or-
Fifa.members( 'Oceania' ).size       #=> 14 members

Fifa.members( 'UEFA' ).size          #=> 55 members  -or-
Fifa.members( 'Europe' ).size        #=> 55 members

...
```

That's it.



## What's FIFA¹?

The World's Football Association / Governing Body
uses its own country list and (three-letter²) codes - see
the [List of FIFA country codes @ Wikipedia](https://en.wikipedia.org/wiki/List_of_FIFA_country_codes)
or the [`countries.txt`](https://github.com/sportdb/football.db/blob/master/fifa/config/countries.txt) list shipping with this library.

Trivia - The FIFA member list includes 211 countries (in 2021) while the United Nations (UN)
member list only includes 193 countries.
For example, for historic reasons the FIFA includes:

- England › UK (ENG)
- Wales › UK (WAL)
- Scotland › UK (SCO)
- Northern Ireland › UK (NIR)

but doesn't include the United Kingdom (UK) itself.
Or the FIFA includes (non-sovereign) dependent territories or autonomous regions
that have their own national team or leagues, for example:

- Gibraltar › UK (GIB)
- Faroe Islands › DK (FRO)
- Hong Kong › CN (HKG)
- Bermuda › UK (BER)
- Puerto Rico › US (PUR)
- and others


Note: This library includes non-FIFA member codes and irregular codes
from countries in use by regional confederations too, for example:

- Vatican City (VAT)
- Monaco (MCO)
- Micronesia (FSM)
- Palau (PLW)
- Réunion › FR (REU)
- Zanzibar › TZ (ZAN)
- Saint Martin › FR (SMN)
- Sint Maarten › NL (SMA)
- and others


¹: [Fédération Internationale de Football Association (FIFA) @ Wikipedia](https://en.wikipedia.org/wiki/FIFA)

²: Northern Cyprus (TRNC)	has a four-letter FIFA country code



## Find Out More

### References

- Home :: [github.com/sportdb/football.db/fifa](https://github.com/sportdb/football.db/tree/master/fifa)
- Gem :: [fifa](https://rubygems.org/gems/fifa)
- Docs :: [fifa](http://rubydoc.info/gems/fifa)

