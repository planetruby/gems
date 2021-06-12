# Day 2 - sportdb-models Gem - Football SQL Schema and ActiveRecord Models for Easy (Re)Use - Inserting the Euro 2020 Opening Match - Turkey vs Italy 0:3 (0:0) - Into the Database "by Hand"


Written by {% avatar geraldb %} [Gerald Bauer](https://github.com/geraldb)



## What's the sportdb-models gem?

The sportdb-models gem ships with an ready-to-use sport.db SQL schema and ActiveRecord models.

![](i/sportdb-models.png)



## Euro 2020 Kick Off Match Sample  -  Turkey vs Italy 0:3 (0:0)


Let's get started with building up an SQL database from scratch
for the
European Football Championship ("Euro") 2020
and let's add the opening match
that reads in the Football.TXT format:


```
(1)  June/11   21:00    Turkey  0-3 (0-0)  Italy     @ Stadio Olimpico, Rome
       [Merih Demiral 53' (o.g.) Ciro Immobile 66' Lorenzo Insigne 79']
```




Step 0 - Auto-Migrate the SQL Database.
Let's build a single-file SQLite database, that is, `euro.db`, from scratch / zero:

``` ruby
require 'sportdb/models'

SportDb.connect!( adapter:  'sqlite3',
                  database: './euro.db' )
```

Note: Using  `connect!` with a bang (`!`)
will "auto-magically" create - or is that auto-migrate -
all database tables if the database is empty, that is, schemaless and without tables.



Let's add the Euro 2020 event
(re)using the ready-to-use ActiveRecord models that
will "auto-magically" create all SQL INSERT/UPDATE statements:


``` ruby
euro2020 = SportDb::Model::Event.create!(
               key: 'euro2020',
               league: SportDb::Model::League.create!( key: 'euro', name: 'Euro' ),
               season: SportDb::Model::Season.create!( key: '2020', name: '2020' ),
               start_date: '2021-06-11',
               end_date:   '2021-07-11'
               )
```


And let's add the two national selection teams, that is, Italy and Turkey:

``` ruby
it = WorldDb::Model::Country.create!(
        key: 'it',
        name: 'Italy',
        code: 'ITA',
        pop:  60_317_116,   # (2020 estimate)
        area: 301_340       # (km2)
        )

tr = WorldDb::Model::Country.create!(
        key: 'tr',
        name: 'Turkey',
        code: 'TUR',
        pop:  83_614_362,  # (2020 estimate)
        area: 783_356      # (km2)
        )


ita = SportDb::Model::Team.create!(
            key: 'ita',
            name: 'Italy',
            code: 'ITA',
            country: it
            )

tur = SportDb::Model::Team.create!(
           key: 'tur',
           name: 'Turkey',
           code: 'TUR',
           country: tr
           )
```

Note: The sport.db schema pulls in, that is, (re)uses
the world.db geo schema for countries, states, cities, etc.



And finally let's add the opening match, that is, Turkey vs Italy:

``` ruby
m =  SportDb::Model::Match.create!(
           event: euro2020,
           pos:   1,
           team1: tur,
           team2: ita,
           date:  '2021-06-11',
           time:  '21:00'
           )
```



And the winner is...
let's update the match scores, that is, 0:3 (0:0):

``` ruby
m.score1 = 0
m.score2 = 3
m.score1i = 0
m.score2i = 0
m.save
```

And to double check - the winner is...

``` ruby
m.winner   #=> 2
```

Team 2, that is, Italy.


Let's add the goals.
Step 1 - let's add the players
pulling in the person.db schema for (re)use:

``` ruby
demiral = PersonDb::Model::Person.create!(
            key: 'merihdemiral',
            name: 'Merih Demiral',
            country_id: tr.id )

immobile = PersonDb::Model::Person.create!(
             key: 'ciroimmobile',
             name: 'Ciro_Immobile',
             country_id: it.id )

insigne = PersonDb::Model::Person.create!(
            key:  'lorenzoinsigne',
            name: 'Lorenzo_Insigne',
            country_id: it.id )
```

And the goals:

``` ruby
SportDb::Model::Goal.create!(
                      person:  demiral,
                      match:   m,
                      team_id: tur.id,
                      minute: 53,
                      score1: 0,
                      score2: 1,
                      owngoal: true
)

SportDb::Model::Goal.create!(
                      person:  immobile,
                      match:   m,
                      team_id: ita.id,
                      minute: 66,
                      score1: 0,
                      score2: 2
)

SportDb::Model::Goal.create!(
                      person:  insigne,
                      match:   m,
                      team_id: ita.id,
                      minute: 79,
                      score1: 0,
                      score2: 3
)
```

And to wrap up let's
add the stadium (also known as ground)
that is,  Stadio Olimpico in Rome:

``` ruby
rome = WorldDb::Model::City.create!(
           key: 'rome',
           name: 'Rome',
           country: it
)

ground = SportDb::Model::Ground.create!(
             key:  'stadioolimpico',
             name: 'Stadio Olimpico',
             city:    rome,
             country: rome.country
          )

m.ground = ground
m.save
```

That's the warm up. Ready? Now it's your turn.
Why not add today's matches, scores and goals?
Bonus points for adding the tournament goups A, B, etc.
The upcoming matches read in the Football.TXT format:


```
Group A:
(2)  June/12  15:00     Wales    -   Switzerland   @ Olympic Stadium, Baku   # 17:00 UTC+4

Group B:
(3)  June/12  18:00     Denmark    -   Finland     @ Parken Stadium, Copenhagen
(4)  June/12  21:00     Belgium    -   Russia      @ Krestovsky Stadium, Saint Petersburg  # 22:00 UTC+3
```


## Find Out More

### References

- Home     :: [sportdb.github.io](http://sportdb.github.io)
- Gem      :: [sportdb-models](https://rubygems.org/gems/sportdb-models)
- Docs     :: [sportdb-models](http://rubydoc.info/gems/sportdb-models)
- Datasets  :: [github.com/openfootball](https://github.com/openfootball)

