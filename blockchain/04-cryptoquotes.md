# Day 4 - cryptoquotes Gem - I HODL, You HODL, We HODL! - BREAKING: BITCOIN JUST BROKE $22 000! - Get Free Oracle Sayings About the New New 'In Math We Trust' Ponzi Economics



Written by {% avatar geraldb %} [Gerald Bauer](https://github.com/geraldb)

_A code monkey formerly at [Big Korupto](https://github.com/bigkorupto). Jobless no coiner having fun staying poor and wrong. Writing the [Get Rich Quick "Business Blockchain" Bible - The Secrets of Free Easy Money](https://bitsblocks.github.io/get-rich-quick-bible)._




> SEC Investor Education:
>
> New Year's Financial Resolution: Avoid too-good-to-be-true "investments" with Ponzi scheme "red flag" claims like:
>
> - "To the moon! To the mars!"
> - "Number go up!"
> - "Yearly return of 300+% in 2020!"
> - "Could quadruple in 2021 and rally to $100,000!"


## What's Crypto Quotes?


The [cryptoquotes gem](https://github.com/rubycoco/blockchain/tree/master/cryptoquotes) includes the oracle command line tool that gets you a free
random crypto quote of the day on the new new "in math we trust" ponzi economics - on get-rich-quick blockchain secrets - on bitcon maximalists, scammers, morons, clowns, shills & bagHODLers and more


Try the `oracle` command line tool:

```
$ oracle -h
```

resulting in:

```
Usage: oracle [options]
  Print wise oracle sayings / crypto quotes

  Options:
    -n, --number=NUM                 Number of quotes to print (default: 1)
    -h, --help                       Prints this help
```


Let's give it a try. Ask the oracle (it's free):

```
$ oracle
```

printing:

```
We have not entered "Post Tether" yet.
Tether is still furiously printing digital bucks.
Who believes Bitcoin's price is organic?
It's not - it's a Ponzi, and Tether is its Little Helper.

> Austrian Maximalist comments:
>
> Bitcoin is not a ponzi. It is a monetary network, first of its kind.
> Currently valued at 365 billion dollars. What am I missing?

    - Amy Castor
```

Or lets try:

```
$ oracle -n2
```

printing:

```
A friend of mine, who works in finance, asked me to explain what Tether was.

Short version: Tether is the internal accounting system for
the largest fraud since Madoff's ponzi scheme.

    - Patrick McKenzie


BREAKING: BITCOIN JUST BROKE $22,000!

We have just reached escape velocity.

> Austrian Maximalist I comments:
>
> 1 Bitcoin = 1 Honda Civic
>
> Soon:
>
> 1 Bitcoin = 1 Tesla, then
> 1 Bitcoin = 1 Lambo, then
> 1 Bitcoin = 1 Super Yacht
>
> Austrian Maximalist II comments:
>
> Just keep HODLing until 1 Bitcoin = 1 Moonbase

    - Bitcoiner
```


## Usage in Your Scripts


Yes, you can add the oracle into your own scripts.
Example:

``` ruby
require 'cryptoquotes'

Oracle.say        # or Oracle.says
```

printing


```
Bitcoin user guide:
1. Buy Bitcoin.
2. Never sell Bitcoin.
3. Don't listen to Bitcoin critics.
4. Tell your friends to buy Bitcoin.
5. Tell your friends never to sell Bitcoin.
6. Tell your friends not to listen to Bitcoin critics.
7. Remember that Bitcoin isn't a pyramid scheme.

> Austrian Maximalist comments:
>   8. See super-rich legendary investors like Tudor Jones, Stanley Druckenmiller,
>      Michael Saylor, etc. who wouldn't fall for mere pyramid schemes, buy and hold Bitcoin.

    - Trolly McTrollface
```
or
```
Right now at the all-time high (ATH) no one who has HODL is down. That's a good feeling.

I HODL, you HODL, we HODL!

> Austrian Maximalist comments:
>
>  I bought at the all-time high (ATH) and I'm UP.

    - Bitcoiner
```

If you want to get quotes in the source format
(in memory)
use the methods from `Quotes`. Example:

```
q = Quotes.random      # get a random quote (as as hash table)
#=> {"quote"=>
#   "Tether is \"too big to fail\" - the entire crypto industry utterly\n" +
#   "depends on it. We just topped twenty billion alleged dollars' worth of tethers ($USDT).\n" +
#   "If you think this is sustainable, you're a fool.\n",
#  "date"=>#<Date: 2020-12-13>,
#  "source"=>"https://twitter.com/davidgerard/status/1338215299737407490",
#  "by"=>"David Gerard"}
```

That's it for now.




## Sources

The crypto quotes sourced from
[100+ Best of Crypto Quotes - I HODL, you HODL, we HODL! - BREAKING: BITCOIN JUST BROKE $22 000!»](https://github.com/openblockchains/crypto-quotes)



## Find Out More

### References

- Home :: [github.com/rubycoco/blockchain/cryptoquotes](https://github.com/rubycoco/blockchain/tree/master/cryptoquotes)
- Gem :: [cryptoquotes](https://rubygems.org/gems/cryptoquotes)
- Docs :: [cryptoquotes](http://rubydoc.info/gems/cryptoquotes)
- Books :: [Best of Crypto Books](https://openblockchains.github.io/crypto-books/)

