# Day 3 - sportdb Gem - Read in Sport (Football) Datasets in (Structured) Text Into Your SQL Database of Choice (SQLite, PostgreSQL, etc) - World Cup, English Premier League, Spanish La Liga, Austrian Bundesliga and More

Written by {% avatar geraldb %} [Gerald Bauer](https://github.com/geraldb)

_A code monkey and enthusiastic collector (and publisher) of open football and beer data. Skills include Ruby, SQLite and CSV. Spec lead of [CSV <3 JSON](https://github.com/csvspecs/csv-json)._




## What's the sportdb gem?

The sportdb command line tool and gems ships with an ready-to-use sport.db SQL schema and ActiveRecord models and (structured) text-to-data readers (parsers).

![](i/sportdb-models.png)



## Quick Starter Sample - Kick Off

Let's get started with the [Quick Starter Sample](https://github.com/openfootball/league-starter). Welcome to the Mauritius Premier Football League.
Lets create your own (structured) text datasets from scratch and read it all
into your SQL database of choice (SQLite, PostgreSQL, etc)
with a single command e.g.:


```
$ sportdb build
```

Let's get started. Follow along these three steps:

- Step 1: Add all leagues
- Step 2: Add all clubs
- Step 3: Add all match fixtures and results
- Let's build. That's it. Done.

Using a file structure like:

```
mauritius
├── 2014-15                # 2014-15 season folder
|   ├── 1-league-i.txt     #   match fixtures / results - matchdays  1-18
|   └── 1-league-ii.txt    #                            - matchdays 19-36
├── leagues.txt            # all leagues
└── clubs.txt              # all clubs
```



## Step 1: Add all leagues

Example - [`leagues.txt`](https://github.com/openfootball/league-starter/blob/master/leagues.txt):

```
= Mauritius =

1  Mauritius Premier League
```

Note: The datafile starts with the country heading, that is, `= ... =`
and than lists all leagues and cups on its own lines.


## Step 2: Add all clubs

Example - [`clubs.txt`](https://github.com/openfootball/league-starter/blob/master/clubs.txt):

```
= Mauritius =

Cercle de Joachim | Cercle de Joachim SC | Joachim
Chamarel | Chamarel SC
Curepipe Starlight | Curepipe SC | Starlight
Entente Boulet Rouge | Entente Boulet Rouge-Riche Mare Rovers
La Cure Sylvester | La Cure
Pamplemousses | Pamplemousses SC
Petite Rivière Noire | Petite Rivière
AS Port-Louis 2000 | ASPL 2000
AS Quatre Bornes | Quatre Bornes
AS Rivière du Rempart | Rivière du Rempart
```

Note: The datafile again starts with the country heading, that is, `= ... =` and than lists
all clubs on its own lines. Use the pipe (`|`) to list alternate club names.


## Step 3: Add all match fixtures and results

Example - [`2014-15/1-league-i.txt`](https://github.com/openfootball/league-starter/blob/master/2014-15/1-league-i.txt):

```
= Mauritius Premier League 2014/15 =

Matchday 1
[Wed Nov/5]
  Curepipe Starlight    1-3  Petite Rivière Noire
  AS Quatre Bornes      1-0  La Cure Sylvester
  Pamplemousses         0-1  Rivière du Rempart
  AS Port-Louis 2000    5-1  Entente Boulet Rouge
  Chamarel SC           2-3  Cercle de Joachim

Matchday 2
[Sun Nov/9]
  Curepipe Starlight    2-1  AS Quatre Bornes
  Entente Boulet Rouge  1-2  Chamarel SC
  Rivière du Rempart    1-1  AS Port-Louis 2000
  La Cure Sylvester     1-2  Pamplemousses
  Petite Rivière Noire  2-0  Cercle de Joachim

Matchday 3
[Wed Nov/12]
  Chamarel SC           1-1  Rivière du Rempart
  AS Port-Louis 2000    1-0  La Cure Sylvester
  Cercle de Joachim     2-2  Entente Boulet Rouge
  Pamplemousses         0-4  Curepipe Starlight
  AS Quatre Bornes      1-2  Petite Rivière Noire


Matchday 4
[Sun Nov/16]
  Petite Rivière Noire  4-1  Entente Boulet Rouge
  Rivière du Rempart    1-1  Cercle de Joachim
  La Cure Sylvester     0-0  Chamarel SC
  Curepipe Starlight    0-0  AS Port-Louis 2000
  AS Quatre Bornes      1-0  Pamplemousses

...
```


## Let's build. That's it. Done.

Now try in your working folder:

```
$ sportdb build
```

This will run the build script and

- setup a new single-file SQLite database e.g. `./sport.db` from scratch (zero)
- read in all datasets in plain text

That's it. Try:

```
$ sqlite3 sport.db

SQLite version 3.7.16
Enter ".help" for instructions
Enter SQL statements terminated with a ";"

sqlite> .tables

alltime_standing_entries  events_grounds            names
alltime_standings         events_teams              parts
assocs                    games                     persons
assocs_assocs             goals                     places
badges                    grounds                   props
cities                    group_standing_entries    rosters
continents                group_standings           rounds
counties                  groups                    seasons
countries                 groups_teams              states
country_codes             langs                     taggings
districts                 leagues                   tags
event_standing_entries    logs                      teams
event_standings           metros                    usages
events                    munis                     zones

sqlite> select * from countries;

1|Mauritius|mauritius|mu|1|MUS|||1261200|2040|||f|t|f|f|

sqlite> select * from teams;

1|joachim|Cercle de Joachim||CDJ|Cercle de Joachim SC|Joachim||t|||||f|
2|chamarel|Chamarel SC||CHA|Chamarel|Chamarel Sport Club||t|||||f|
3|curepipesc|Curepipe Starlight||CUR|Curepipe Starlight SC||t|||||f|
4|entente|Entente Boulet Rouge||EBR|Entente Boulet Rouge SC|Entente Boulet Rouge-Riche Mare Rovers||t|||||f|
5|lacure|La Cure Sylvester||LCS|La Cure Sylvester SC|La Cure||t|||||f|
6|pamplemousses|Pamplemousses||PPM|Pamplemousses SC||t|||||f|
7|petiteriv|Petite Rivière Noire||PRN|Petite Rivière Noire SC|Petite Rivière||t|||||f|
8|aspl|AS Port-Louis 2000||APL|ASPL 2000|Port-Louis 2000|Association Sportive Port-Louis 2000||t|||||f|
9|qbornes|AS Quatre Bornes||AQB|ASQB|Quatre Bornes||t|||||f|
10|rempart|Rivière du Rempart||ARR|AS Rivière du Rempart||t|||||f|
11|pointauxsables|Pointe-aux-Sables Mates|||||t|||||f|
12|savanne|Savanne SC|||Savanne Sporting Club||t|||||f|
```

And so on.



## Bonus: Run Your Own HTTP JSON APIs / Services


You can run any of the HTTP JSON API (web service) scripts using the `sportdb` command line tool.
By default the `serve` command will look for
a script named `Service` or `service.rb` (in the working folder, that is, `./`). Example:

```
$ sportdb serve
```

To run any other script - copy the script into the working folder and pass it along as an argument. Example:

```
$ sportdb serve starter      #  note: will (auto-)add the .rb extension  or
$ sportdb serve starter.rb
```



## Find Out More

### References

- Home     :: [sportdb.github.io](http://sportdb.github.io)
- Gem      :: [sportdb](https://rubygems.org/gems/sportdb)
- Docs     :: [sportdb](http://rubydoc.info/gems/sportdb-models)
- Datasets  :: [github.com/openfootball](https://github.com/openfootbal)

