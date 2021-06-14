# Day 4 - football-to-sqlite Gem - Read the European Football Championship ("Euro") 2020 Match Schedule in the Football.TXT Format Into euro.db Using the Shell / Command Line


Written by {% avatar geraldb %} [Gerald Bauer](https://github.com/geraldb)



## What's the football-to-sqlite gem?

The football-to-sqlite gem ships with the `football-to-sqlite` (or `football2sqlite`)
command line tool that lets you
read match files in the (structured) Football.TXT format
into single-file SQLite databases.



Let's try:

```
$ football-to-sqlite --help
```

resulting in

```
Usage: football-to-sqlite [options] DATABASE PATHS...
```


Yes, that's it.
Pass along the database name e.g. `euro2020.db`
and one or more match files e.g. `euro2020.txt`
and you're done.



## Euro 2020 - 24 National Teams, 6 Groups - June 11th to July 11th 2021


Let's building up an SQLite database from scratch / zero
for the
European Football Championship ("Euro") 2020
reading in the match schedule in the Football.TXT format.


Grab / download a copy from the
open public domain football datasets org
@
[`/euro/2020--europe/euro.txt`](https://github.com/openfootball/euro/raw/master/2020--europe/euro.txt) of the match schedule to your own local computer.
And run the command line tool against the match file:

```
$ football-to-sqlite euro2020.db euro.txt
```

Voila. That's it.  The `euro2020.db` single-file SQLite database is now
populated with 24 national teams in 6 groups and 36 matches (in the group phase)
and so on.


Note: The command line tool will "auto-magically" create
the database with all tables, indices, etc. on the first run if the single-file database
does not (yet) exist.



**Bonus - Pipes & Standard Input (STDIN)**

Yes, you can use any command line tool to download match files and pipe (via stdin) into the tool e.g.:

```
$ curl https://github.com/openfootball/euro/raw/master/2020--europe/euro.txt | football-to-sqlite euro2020.db
```


## Limitations

Note: For now you CANNOT update match files, that is,
if you try to add the same match twice (assuming with updated scores or such), the match reader will fail.
The workaround is to always re-create/re-build your database from zero / scratch.





## Find Out More

### References

- Home     :: [sportdb.github.io](http://sportdb.github.io)
- Gem      :: [football-to-sqlite](https://rubygems.org/gems/football-to-sqlite)
- Docs     :: [football-to-sqlite](http://rubydoc.info/gems/football-to-sqlite)
- Datasets  :: [github.com/openfootball](https://github.com/openfootball)

