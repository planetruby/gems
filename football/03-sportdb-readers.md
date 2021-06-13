# Day 3 - sportdb-readers Gem - Read the European Football Championship ("Euro") 2020 Match Schedule in the Football.TXT Format Into euro.db - A Single-File SQLite Database


Written by {% avatar geraldb %} [Gerald Bauer](https://github.com/geraldb)



## What's the sportdb-readers gem?

The sportdb-readers gem let's you read in football match schedules and results, teams, groups, and more
in the (structured) Football.TXT format into any SQL database of your choice
(e.g. SQLite, PostgreSQL, etc.)




## Euro 2020 - 24 National Teams, 6 Groups - June 11th to July 11th 2021


Let's get started with building up an SQL database from scratch
for the
European Football Championship ("Euro") 2020
and let's read in the match schedule in the Football.TXT format.


Step 0 - Auto-Migrate the SQL Database.
Let's build a single-file SQLite database, that is, `euro.db`, from scratch / zero:

``` ruby
require 'sportdb/readers'

SportDb.connect!( adapter:  'sqlite3',
                  database: './euro.db' )
```

Note: Using  `connect!` with a bang (`!`)
will "auto-magically" create - or is that auto-migrate -
all database tables if the database is empty, that is, schemaless and without tables.



Let's add the Euro 2020 event:

``` ruby
SportDb::MatchReader.parse( <<TXT )
= Euro 2021

Group A | Turkey        Italy      Wales      Switzerland
Group B | Denmark       Finland    Belgium    Russia
Group C | Netherlands   Ukraine    Austria    North Macedonia
Group D | England       Croatia    Scotland   Czech Republic
Group E | Spain         Sweden     Poland     Slovakia
Group F | Hungary       Portugal   France     Germany


Matchday 1 | Jun/11 - Jun/15
Matchday 2 | Jun/16 - Jun/19
Matchday 3 | Jun/20 - Jun/23


Group A:

(1)  June/11   21:00    Turkey   0-3 (0-0)   Italy         @ Stadio Olimpico, Rome
        [Demiral 53' (o.g.) Immobile 66' Insigne 79']
(2)  June/12   15:00    Wales    1-1 (0-0)   Switzerland   @ Olympic Stadium, Baku   # 17:00 UTC+4
        [Moore 74'; Embolo 49']

(13) June/16   18:00    Turkey   -   Wales         @ Olympic Stadium, Baku   # 20:00 UTC+4
(14) June/16   21:00    Italy    -   Switzerland   @ Stadio Olimpico, Rome

(25) June/20   18:00    Switzerland  - Turkey      @ Olympic Stadium, Baku   # 20:00 UTC+4
(26) June/20   18:00    Italy        - Wales       @ Stadio Olimpico, Rome


Group B:

(3)  June/12  18:00     Denmark    0-1 (0-0)   Finland     @ Parken Stadium, Copenhagen
         [Pohjanpalo 60']
(4)  June/12  21:00     Belgium    3-0 (2-0)   Russia      @ Krestovsky Stadium, Saint Petersburg  # 22:00 UTC+3
         [Lukaku 10', 88' Meunier 34']

(15) June/16  15:00     Finland    -   Russia      @ Krestovsky Stadium, Saint Petersburg  # 16:00 UTC+3
(16) June/17  18:00     Denmark    -   Belgium     @ Parken Stadium, Copenhagen

(27) June/21  21:00     Russia     -   Denmark     @ Parken Stadium, Copenhagen
(28) June/21  21:00     Finland    -   Belgium     @ Krestovsky Stadium, Saint Petersburg   # 22:00 UTC+3


Group C:

(6)  June/13  18:00    Austria      -   North Macedonia   @ Arena Națională, Bucharest   # 19:00 UTC+3
(5)  June/13  21:00    Netherlands  -   Ukraine           @ Johan Cruyff Arena, Amsterdam

(18) June/17  15:00    Ukraine      -   North Macedonia   @ Arena Națională, Bucharest   # 16:00 UTC+3
(17) June/17  21:00    Netherlands  -   Austria           @ Johan Cruyff Arena, Amsterdam

(29) June/21  18:00    North Macedonia  -   Netherlands   @ Johan Cruyff Arena, Amsterdam
(30) June/21  18:00    Ukraine          -   Austria       @ Arena Națională, Bucharest   # 19:00 UTC+3


Group D:

(7)  June/13  15:00    England         -  Croatia         @ Wembley Stadium, London  # 14:00 UTC+1
(8)  June/14  15:00    Scotland        -  Czech Republic  @ Hampden Park, Glasgow   # 14:00 UTC+1

(19) June/18  18:00    Croatia         -  Czech Republic  @ Hampden Park, Glasgow  # 17:00 UTC+1
(20) June/18  21:00    England         -  Scotland        @ Wembley Stadium, London  # 20:00 UTC+1

(31) June/22  21:00    Croatia         -  Scotland        @ Hampden Park, Glasgow  # 20:00 UTC+1
(32) June/22  21:00    Czech Republic  -  England         @ Wembley Stadium, London  # 20:00 UTC+1


Group E:

(10) June/14  18:00    Poland          -  Slovakia        @ Krestovsky Stadium, Saint Petersburg   # 19:00 UTC+3
(9)  June/14  21:00    Spain           -  Sweden          @ La Cartuja, Seville

(21) June/18  15:00    Sweden          -  Slovakia        @ Krestovsky Stadium, Saint Petersburg   # 16:00 UTC+3
(22) June/19  21:00    Spain           -  Poland          @ La Cartuja, Seville

(33) June/23  18:00    Slovakia        -  Spain           @ La Cartuja, Seville
(34) June/23  18:00    Sweden          -  Poland          @ Krestovsky Stadium, Saint Petersburg   # 19:00 UTC+3


Group F:

(11) June/15  18:00    Hungary         -  Portugal        @ Puskás Aréna, Budapest
(12) June/15  21:00    France          -  Germany         @ Allianz Arena, Munich

(23) June/19  15:00    Hungary         -  France          @ Puskás Aréna, Budapest
(24) June/19  18:00    Portugal        -  Germany         @ Allianz Arena, Munich

(35) June/23  21:00    Portugal        -  France          @ Puskás Aréna, Budapest
(36) June/23  21:00    Germany         -  Hungary         @ Allianz Arena, Munich
TXT
```



Yes, all teams, groups, match days, match fixtures and results, and more are now in your (SQL) database of choice, that is, in `euro.db` - a single-file SQLite database.


The proof of the pudding. Let's query the (SQL) database using the ActiveRecord models:


``` ruby
include SportDb::Models

euro  = Event.find_by( key: 'euro.2021' )
euro.teams.count  #=> 24
euro.groups.count #=> 6
matches = euro.matches
matches.count     #=> 36
```

Resulting in a team count of 24 in 6 groups and
36 matches in the group phase for the Euro 2020 event.


Let's print the kick off opening match using
a little pretty print helper:

``` ruby
def print_match( m )
   print  "(#{m.pos}) "
   print  "#{m.date.strftime( '%a, %d %b')}  "
   print  '%22s' % "#{m.team1.name} (#{m.team1.code})"

   if m.score1 && m.score2
     print  "  #{m.score1}-#{m.score2} (#{m.score1i}-#{m.score2i})  "
   else
     print  "  vs  "
   end

   print  '%-22s' % "#{m.team2.name} (#{m.team2.code})"
   print  ' / '
   print  m.group.name
   print  ' - '
   print  m.round.name
   print "\n"
end
```

And

``` ruby
print_match( matches[0] )
```

resulting in:

```
(1) Fri, 11 Jun            Turkey (TUR)  0-3 (0-0)  Italy (ITA)            / Group A - Matchday 1
```



Let's get all matches for Austria (AUT):

``` ruby
aut = Team.find_by( key: 'aut' )
matches = aut.matches
matches.count   #=> 3

print_match( matches[0] )
print_match( matches[1] )
print_match( matches[2] )
```

resulting in:

```
(13) Sun, 13 Jun           Austria (AUT)  vs  North Macedonia (MKD)  / Group C - Matchday 1
(16) Thu, 17 Jun       Netherlands (NED)  vs  Austria (AUT)          / Group C - Matchday 2
(18) Mon, 21 Jun           Ukraine (UKR)  vs  Austria (AUT)          / Group C - Matchday 3
```


And to wrap up let's get
all matches for today (that is, Sun, 13 June, 2021):

``` ruby
matches = Match.where( date: '2021-06-13 ')
matches.count  #=> 3

print_match( matches[0] )
print_match( matches[1] )
print_match( matches[2] )
```

resulting in:

```
(13) Sun, 13 Jun           Austria (AUT)  vs  North Macedonia (MKD)  / Group C - Matchday 1
(14) Sun, 13 Jun       Netherlands (NED)  vs  Ukraine (UKR)          / Group C - Matchday 1
(19) Sun, 13 Jun           England (ENG)  vs  Croatia (CRO)          / Group D - Matchday 1
```

What's your predication for today? What's your favorite national team?
Enjoy the beautiful game.




## Find Out More

### References

- Home     :: [sportdb.github.io](http://sportdb.github.io)
- Gem      :: [sportdb-readers](https://rubygems.org/gems/sportdb-readers)
- Docs     :: [sportdb-readers](http://rubydoc.info/gems/sportdb-readers)
- Datasets  :: [github.com/openfootball](https://github.com/openfootball)


