# Day 2 - beerdb Gem - Serve a Guinness Irish Stout or a Bamberg Aecht Schlenkerla Rauchbier Märzen as JSON - Free Home Delivery


Written by {% avatar geraldb %} [Gerald Bauer](https://github.com/geraldb)

_A code monkey and enthusiastic collector (and publisher) of open football and beer data. Skills include Ruby, SQLite and CSV. Spec lead of [CSV <3 JSON](https://github.com/csvspecs/csv-json)._




## What's the beerdb gem?

The beerdb gem offers a ready-to-use database schema (in SQL) and models such as - surprise, surprise -
`Beer`, `Brewery`, `Brand` and friends (using the `ActiveRecord` object-relational mapper machinery). Example:

![](i/beerdb-models.png)


Let's try the brewery model:

``` ruby
by = Brewery.find_by( key: 'guinness' )

by.title
#=> 'St. James's Gate Brewery / Guinness Brewery'

by.country.key
#=> 'ie'

by.country.title
#=> 'Ireland'

by.city.title
#=> 'Dublin'

by.beers.first
#=> 'Guinness', 4.2

...
```

Or let's try the beer model:


``` ruby
b = Beer.find_by( key: 'guinness' )

b.title
#=> 'Guinness'

b.abv    # that is, alcohol by volume (abv)
#=> 4.2

b.tags
#=> 'irish_dry_stout', 'dry_stout', 'stout'

b.brewery.title
#=> 'St. James's Gate Brewery / Guinness Brewery'
...
```

What's it good for? Good question. Let's build an HTTP JSON service
that serves up a Guinness Irish Stout
or a Bamberg Aecht Schlenkerla Rauchbier Märzen as JSON?
Example - `GET /beer/guinness`:

``` json
{
  "key": "guinness",
  "title": "Guinness",
  "synonyms": "Guinness Draught",
  "abv": "4.2",
  "srm": null,
  "og": null,
  "tags": ["irish_dry_stout","dry_stout","stout"],
  "brewery":
  {
    "key": "guinness",
    "title": "St. James's Gate Brewery / Guinness Brewery"
  },
  "country":
  {
    "key": "ie",
    "title": "Irland"
  }
}
```


Let's use Sinatra - a micro webframework - that offers a mini language, that is, domain-specific language (DSL)
that lets you define routes, that is, HTTP methods paired with an URL-machting pattern and much more.
For example, you can code the `GET /beer/guinness` route in
Sinatra as `get '/beer/guinness'`. To make it into a route for any beer lets replace the `guinness` beer key
with a placeholder, thus, resulting in `get '/beer/:key'`. Let's try -`service.rb`:


``` ruby
class BeerService < Sinatra::Base

  include BeerDb::Models     # lets (re)use the Beer, Brewery, etc. models

  get '/beer/:key' do |key|
    beer = Beer.find_by!( key: key )

    content_type :json       # set response to JavaScript Object Notation (JSON) content type

    { key:      beer.key,
      title:    beer.title,
      synonyms: beer.synonyms,
      abv:      beer.abv,
      srm:      beer.srm,
      og:       beer.og  }.to_json
  end

end
```

That's it. Ready to serve. Let's boot-up the beer service
with a web server (e.g. Thin) using a Rack handler. Example - `boot.rb`:

``` ruby
require 'sinatra/base'    # note: sinatra will pull in web server machinery (e.g. rack, thin, etc.)
require 'beerdb/models'   # note: beerdb will pull in database access machinery (e.g. activerecord, etc.)

# database setup 'n' config
ActiveRecord::Base.establish_connection( adapter:  'sqlite3',
                                         database: './beer.db' )


require './service'

Rack::Handler::Thin.run BeerService.new, :Port => 9292
```

Try:

```
$ ruby ./boot.rb
```

Resulting in:

```
Thin web server (v1.6.3 codename Protein Powder)
Maximum connections set to 1024
Listening on 0.0.0.0:9292, CTRL+C to stop
```

Open up your browser and try `http://localhost:9292/beer/guinness`.
Voila. Enjoy the Guinness responsibly.
Cheers, Prost, Kampai, Na zdravi, Salute, 乾杯, Skål, Egészségedre!



## Bonus:  Let's add brewery details and more

Let's add brewery details to the beer service and lets add a new `GET /brewery` route. Example:

``` ruby
get '/beer/:key' do |key|
  beer = Beer.find_by!( key: key )

  brewery = {}
  if beer.brewery.present?
     brewery = { key:   beer.brewery.key,
                 title: beer.brewery.title }
  end

  tags = []
  if beer.tags.present?
     beer.tags.each { |tag| tags << tag.key }
  end

  country = {
    key:   beer.country.key,
    title: beer.country.title
  }

  content_type :json

  { key:      beer.key,
    title:    beer.title,
    synonyms: beer.synonyms,
    abv:      beer.abv,
    srm:      beer.srm,
    og:       beer.og,
    tags:     tags,
    brewery: brewery,
    country: country }.to_json
end


get '/brewery/:key' do |key|

  brewery = Brewery.find_by!( key: key )

  beers = []
  brewery.beers.each do |b|
    beers << { key: b.key, title: b.title }
  end

  tags = []
  if brewery.tags.present?
     brewery.tags.each { |tag| tags << tag.key }
  end

  country = {
    key:   brewery.country.key,
    title: brewery.country.title
  }

  content_type :json

  { key:      brewery.key,
    title:    brewery.title,
    synonyms: brewery.synonyms,
    since:    brewery.since,
    address:  brewery.address,
    web:      brewery.web,
    tags:     tags,
    beers:    beers,
    country:  country}.to_json
end
```


## Bonus II: Let's use the json helper from sinatra contrib

If you think the code looks to enterprisey (that is, too complex):

``` ruby
get '/beer/:key' do |key|
  beer = Beer.find_by!( key: key )

  content_type :json       # set response to JavaScript Object Notation (JSON) content type

  { key:      beer.key,
    title:    beer.title,
    synonyms: beer.synonyms,
    abv:      beer.abv,
    srm:      beer.srm,
    og:       beer.og  }.to_json
end
```

Let's make it simpler using the json helper from the sinatra contrib package. In your boot script
(e.g. `boot.rb`) add:

``` ruby
require 'sinatra/json'
```

And than you can use:

``` ruby
get '/beer/:key' do |key|
  beer = Beer.find_by!( key: key )

  json key:      beer.key,
       title:    beer.title,
       synonyms: beer.synonyms,
       abv:      beer.abv,
       srm:      beer.srm,
       og:       beer.og
end
```

The json helper will automatically set the json content type and call the `#to_json` method on the passed in data hash.
More magic. Less configuration (code).



## Find Out More

### References

- Home     :: [github.com/beerkit/beer.db](https://github.com/beerkit/beer.db)
- Gem      :: [beerdb](https://rubygems.org/gems/beerdb)
- Docs     :: [beerdb](http://rubydoc.info/gems/beerdb-models)
- Datasets  :: [github.com/openbeer](https://github.com/openbeer)

