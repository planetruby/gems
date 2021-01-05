# Day 3 - universum Gem - Operate the Ponzi Governmental - Real World Case Study - Last Creditor (Before Collapse) Wins the Jackpot!


Written by {% avatar geraldb %} [Gerald Bauer](https://github.com/geraldb)

_A code monkey formerly at [Big Korupto](https://github.com/bigkorupto). Jobless no coiner having fun staying poor and wrong. Writing the [Get Rich Quick "Business Blockchain" Bible - The Secrets of Free Easy Money](https://bitsblocks.github.io/get-rich-quick-bible)._



## Ponzis and Pyramids

### Simple Ponzi - Investment of a Lifetime!

Let's start with a simple ponzi scheme contract - coded in
[small, smart, secure, safe, solid & sound (S6) ruby](https://github.com/s6ruby)
running on the [universum personal blockchain runtime](https://github.com/s6ruby/universum):


``` ruby
class SimplePonzi < Contract

  def initialize
    @current_investor   = msg.sender
    @current_investment = 0
  end

  def receive
    # note: new investments must be 10% greater than current
    minimum_investment = @current_investment * 11 / 10
    assert( msg.value > minimum_investment )

    # record new investor
    previous_investor   = @current_investor
    @current_investor   = msg.sender
    @current_investment = msg.value

    # pay out previous investor
    previous_investor.send( msg.value )
  end

end # class SimplePonzi
```

(Source: [`ponzi_simple.rb`](https://github.com/openblockchains/programming-crypto-contracts/blob/master/ponzi_simple.rb))


What's a ponzi scheme?

> A Ponzi scheme, or "ponzi" for short, is a type of investment fraud with five
> features:
> 1. People invest into it because they expect good profits, and
> 2. that expectation is sustained by such profits being paid to those who choose to cash out. However,
> 3. there is no external source of revenue for those payoffs. Instead,
> 4. the payoffs come entirely from new investment money, while
> 5. the operators take away a large portion of this money.
>
> -- [Bitcoin is a Ponzi](https://www.ic.unicamp.br/~stolfi/bitcoin/2020-12-31-bitcoin-ponzi.html)



Anyways, let's look at the first simple ponzi contract script.
The idea is:

The money sent in by the latest investor
gets paid out to the previous investor and because every
new investment must be at least 10% larger than the last
investment - EVERY INVESTOR WINS! (*)

(*): Except the last "sucker" is HODLing the bag waiting for a greater fool.


Let's setup some test accounts with funny money:

``` ruby
## setup test accounts with starter balance
Account[ '0x1111' ].balance = 0
Account[ '0xaaaa' ].balance = 1_000_000
Account[ '0xbbbb' ].balance = 1_200_000
Account[ '0xcccc' ].balance = 1_400_000

## (pp) pretty print  all known accounts with balance
pp Uni.accounts      # Uni (short for) Universum
```

(Source: [`run_ponzi_simple.rb`](https://github.com/openblockchains/programming-crypto-contracts/blob/master/run_ponzi_simple.rb))


printing:

```
[#<Account @address="0x1111", @balance=0>,
 #<Account @address="0xaaaa", @balance=1000000>,
 #<Account @address="0xbbbb", @balance=1200000>,
 #<Account @address="0xcccc", @balance=1400000>]
 ```

Note: The contract scripts run on Universum - a 3rd generation blockchain / world computer. New to Universum? See the [Universum (World Computer) White Paper](https://github.com/openblockchains/universum/blob/master/WHITEPAPER.md)!



And let's invest:

``` ruby
## genesis - create contract
ponzi = Uni.send_transaction( from: '0x1111', data: SimplePonzi ).contract
pp ponzi
#=> #<SimplePonzi @current_investment=0, @current_investor="0x1111">

Uni.send_transaction( from: '0xaaaa', to: ponzi, value: 1_000_000 )
pp ponzi
#=> #<SimplePonzi @current_investment=1000000, @current_investor="0xaaaa">

Uni.send_transaction( from: '0xbbbb', to: ponzi, value: 1_200_000 )
pp ponzi
#=> #<SimplePonzi @current_investment=1200000, @current_investor="0xbbbb">

Uni.send_transaction( from: '0xcccc', to: ponzi, value: 1_400_000 )
pp ponzi
#=> #<SimplePonzi @current_investment=1400000, @current_investor="0xcccc">

## (pp) pretty print all known accounts with balance
pp Uni.accounts
```

Resulting in:

```
[#<Account @address="0x1111", @balance=1000000>,
 #<Account @address="0xaaaa", @balance=1200000>,
 #<Account @address="0xbbbb", @balance=1400000>,
 #<Account @address="0xcccc", @balance=0>]
```

The "Genesis" `0x1111` account made a 100% profit of 1_000_000.
The `0xaaaa` account made an investment of 1_000_000 and got 1_200_000. 200_000 profit! Yes, it works!
The `0xbbbb` account made an investment of 1_200_000 and got 1_400_000. 200_000 profit! Yes, it works!
The `0xcccc` account is still waiting for a greater fool and is HODLing the bag.
To the moon! Number go up!




##  Ponzi Governmental - Real World Case Study - Last Creditor (Before Collapse) Wins the Jackpot!

Let's look at a real world ponzi (scheme) contract called Governmental
put together by an Austrian School of Economics fan boy or girl.


What's Governmental?

The gambling website says:

> This is an educational game which simulates the finances of a government -
> in other words: It's a Ponzi scheme
>
> Rule 1
>
> You can lend the government money - they promise to pay it back +10% interest.
> Minimum contribution is 1 ETH.
>
> Rule 2
>
> If the government does not receive new money for 12h the system breaks down. The latest
> creditor saw the crash coming and receives the jackpot. All others will lose their claims.
>
> Rule 3
>
> All incoming money is used in the following way: 5% goes into the "jackpot" (capped at 10 000 ETH),
> 5% goes to the corrupt elite that runs the government,
> 90% are used to pay out creditors in order of their date of credit.
> When the jackpot is full, 95% go toward the payout of creditors.
>
> Rule 4
>
> Creditors can share an affiliate link. Money deposited this way is distributed
> as follows: 5% go toward the linker directly, 5% to the corrupt elite,
> 5% into the jackpot (until full). The rest is used for payouts.
>
> (Source: [`governmental.github.io/GovernMental`](http://governmental.github.io/GovernMental/)


Believe it or not the contract ran in early 2016 for about 40 days on
the Ethereum blockchain and
was so popular that the payout money got stuck because of too many investors
and the payout function was hitting the gas limit
(that is, maximum number of instructions).
Read the official announcement titled
[">1000 ETH Jackpot! New Contract - "Governmental" Government Simulation"](https://bitcointalk.org/index.php?topic=1424324.0)

Anyways, let's look at the code (in JavaScript-like Solidity),
see [etherscan.io/address/0xF45717552f12Ef7cb65e95476F217Ea008167Ae3#code](https://etherscan.io/address/0xF45717552f12Ef7cb65e95476F217Ea008167Ae3#code):

``` solidity
contract Government {

    // Global Variables
    uint32 public lastCreditorPayedOut;
    uint public lastTimeOfNewCredit;
    uint public profitFromCrash;
    address[] public creditorAddresses;
    uint[] public creditorAmounts;
    address public corruptElite;
    mapping (address => uint) buddies;
    uint constant TWELVE_HOURS = 43200;
    uint8 public round;

    function Government() {
        // The corrupt elite establishes a new government
        // this is the commitment of the corrupt Elite - everything that can not be saved from a crash
        profitFromCrash = msg.value;
        corruptElite = msg.sender;
        lastTimeOfNewCredit = block.timestamp;
    }

    function lendGovernmentMoney(address buddy) returns (bool) {
        uint amount = msg.value;
        // check if the system already broke down.
        // If for 12h no new creditor gives new credit to the system it will brake down.
        // 12h are on average = 60*60*12/12.5 = 3456
        if (lastTimeOfNewCredit + TWELVE_HOURS < block.timestamp) {
            // Return money to sender
            msg.sender.send(amount);
            // Sends all contract money to the last creditor
            creditorAddresses[creditorAddresses.length - 1].send(profitFromCrash);
            corruptElite.send(this.balance);
            // Reset contract state
            lastCreditorPayedOut = 0;
            lastTimeOfNewCredit = block.timestamp;
            profitFromCrash = 0;
            creditorAddresses = new address[](0);
            creditorAmounts = new uint[](0);
            round += 1;
            return false;
        }
        else {
            // the system needs to collect at least 1% of the profit from a crash to stay alive
            if (amount >= 10 ** 18) {
                // the System has received fresh money, it will survive at leat 12h more
                lastTimeOfNewCredit = block.timestamp;
                // register the new creditor and his amount with 10% interest rate
                creditorAddresses.push(msg.sender);
                creditorAmounts.push(amount * 110 / 100);
                // now the money is distributed
                // first the corrupt elite grabs 5% - thieves!
                corruptElite.send(amount * 5/100);
                // 5% are going into the economy
                //  (they will increase the value for the person seeing the crash comming)
                if (profitFromCrash < 10000 * 10**18) {
                    profitFromCrash += amount * 5/100;
                }
                // if you have a buddy in the government (and he is in the creditor list)
                // he can get 5% of your credits. Make a deal with him.
                if(buddies[buddy] >= amount) {
                    buddy.send(amount * 5/100);
                }
                buddies[msg.sender] += amount * 110 / 100;
                // 90% of the money will be used to pay out old creditors
                if (creditorAmounts[lastCreditorPayedOut] <= address(this).balance - profitFromCrash) {
                    creditorAddresses[lastCreditorPayedOut].send(creditorAmounts[lastCreditorPayedOut]);
                    buddies[creditorAddresses[lastCreditorPayedOut]] -= creditorAmounts[lastCreditorPayedOut];
                    lastCreditorPayedOut += 1;
                }
                return true;
            }
            else {
                msg.sender.send(amount);
                return false;
            }
        }
    }

    // fallback function
    function() {
        lendGovernmentMoney(0);
    }

    function totalDebt() returns (uint debt) {
        for(uint i=lastCreditorPayedOut; i<creditorAmounts.length; i++){
            debt += creditorAmounts[i];
        }
    }

    function totalPayedOut() returns (uint payout) {
        for(uint i=0; i<lastCreditorPayedOut; i++){
            payout += creditorAmounts[i];
        }
    }

    // better don't do it (unless you are the corrupt elite and you want to establish trust in the system)
    function investInTheSystem() {
        profitFromCrash += msg.value;
    }

    // From time to time the corrupt elite inherits it's power to the next generation
    function inheritToNextGeneration(address nextGeneration) {
        if (msg.sender == corruptElite) {
            corruptElite = nextGeneration;
        }
    }
}
```

Let's convert the contract code to ruby and run it to decipher (and understand) the magic money machine!

``` ruby
class Governmental < Contract

  payable :initialize
  payable :lend_government_money, Address => Bool
  payable :invest_in_the_system

  MINIMUM_INVESTMENT = 1_000_000
  TWELVE_HOURS       = 43_200     ## in seconds e.g. 12h*60min*60secs

  def initialize
    ## The corrupt elite establishes a new government
    ## this is the commitment of the corrupt Elite - everything that can not be saved from a crash
    @profit_from_crash       = msg.value
    @corrupt_elite           = msg.sender
    @last_time_of_new_credit = block.timestamp

    @creditor_addresses = []   # Array.of( Address )
    @creditor_amounts   = []   # Array.of( Money   )
    @buddies            = Mapping.of( Address => Money )

    @round = 0
    @last_creditor_paid_out = 0
  end

  def lend_government_money( buddy )
    amount = msg.value

    ## check if the system already broke down.
    ## If for 12h no new creditor gives new credit to the system it will brake down.
    ## 12h are on average = 12h*60min*60secs/12.5 = 3456 blocks
    if @last_time_of_new_credit + TWELVE_HOURS < block.timestamp
      ## Return money to sender
      msg.sender.send( amount )
      ## Sends all contract money to the last creditor
      @creditor_addresses[ @creditor_addresses.length-1].send( @profit_from_crash )
      @corrupt_elite.send( balance )
      ## Reset contract state
      @last_creditor_paid_out = 0
      @last_time_of_new_credit = block.timestamp
      @profit_from_crash = 0
      @creditor_addresses = []
      @creditor_amounts   = []
      @round += 1
      return false
    else
      ## the system needs to collect at least 1% of the profit from a crash to stay alive
      if amount >= MINIMUM_INVESTMENT
        ## the System has received fresh money, it will survive at leat 12h more
        @last_time_of_new_credit = block.timestamp;
        ## register the new creditor and his amount with 10% interest rate
        @creditor_addresses.push( msg.sender )
        @creditor_amounts.push( amount * 110 / 100 )

        ## now the money is distributed
        ## first the corrupt elite grabs 5% - thieves!
        @corrupt_elite.send( amount * 5/100 )

        ## 5% are going into the economy (they will increase the value for the person seeing the crash comming)
        if @profit_from_crash < 10_000 * MINIMUM_INVESTMENT
          @profit_from_crash += amount * 5/100
        end

        ## if you have a buddy in the government (and he is in the creditor list) he can get 5% of your credits.
        ## Make a deal with him.
        if @buddies[buddy] >= amount
          buddy.send( amount * 5/100 )
        end

        @buddies[ msg.sender ] += amount * 110 / 100

        ## 90% of the money will be used to pay out old creditors
        if @creditor_amounts[ @last_creditor_paid_out] <= balance - @profit_from_crash
          @creditor_addresses[ @last_creditor_paid_out ].send( @creditor_amounts[@last_creditor_paid_out] )

          @buddies[ @creditor_addresses[ @last_creditor_paid_out ]] = 0
          @last_creditor_paid_out += 1
        end
        return true
      else
        msg.sender.send( amount )
        return false
      end
    end
  end # method lend_government_money

  def receive
    lend_government_money( '0x0000' )
  end

  def total_debt
    debt = 0
    (@last_creditor_paid_out...@creditor_amounts.length).each do |i|
      debt += @creditor_amounts[i]
    end
    debt
  end

  def total_paid_out
    payout = 0
    (0...@last_creditor_paid_out).each do |i|
      payout += @creditor_amounts[i]
    end
    payout
  end

  ## better don't do it (unless you are the corrupt elite and you want to establish trust in the system)
  def invest_in_the_system
    @profit_from_crash += msg.value
  end

  ## From time to time the corrupt elite inherits it's power to the next generation
  def inherit_to_next_generation( next_generation )
    if msg.sender == @corrupt_elite
      @corrupt_elite = next_generation
    end
  end
end   # class Governmental
```

(Source: [`ponzi_governmental.rb`](https://github.com/openblockchains/programming-crypto-contracts/blob/master/ponzi_governmental.rb))


Now doesn't the new contract script look better, that is, more understandable?
Let's go through the
state (storage) variables setup in the constructor:

`@last_creditor_paid_out` - Stores the array index of the first creditor
that hasn't been paid out. Starting with `0`.  The index is used
with the `@creditor_addresses`
and `@creditor_amounts` arrays.

`@last_time_of_new_credit` - Stores the `block.timestamp`, that is,
the last timestamp of the last creditor's block timestamp.
The timestamp is in seconds since Jan 1st, 1970
(also known as Unix Epoch Time - e.g. print the current Epoch
Time with `Time.now.to_i` resulting in `1549040124` on Feb 1st, 2019 @ 16:55.24).

`@profit_from_crash` - Store the jackpot value! Starting with a
seed down payment / investment of the contract owner
(also known as `@corrupt_elite`)
and gets bigger by 5% for every new creditor.

`@creditor_addresses` and
`@creditor_amounts` - Store the creditors account addresses (e.g. `0xaaaa`, `0xbbbb`, etc.)
and the investment with the 10% promised interest / profit.

`@corrupt_elite` - Holds the contract's owners account address.
Gets paid out 5% from every new investment by new creditors.

`@round` - Starts with `0` and every time
the scheme collapses and the jackpot gets paid out
and new round begins.


Enough theory. Let's run the contract.
Let's setup test accounts with a starter balance:

``` ruby
Account[ '0x1111' ].balance = 1_000_000
Account[ '0xaaaa' ].balance = 1_000_000
Account[ '0xbbbb' ].balance = 1_000_000
Account[ '0xcccc' ].balance = 1_000_000
Account[ '0xdddd' ].balance = 1_000_000
Account[ '0xeeee' ].balance = 1_000_000

## (pp) pretty print all known accounts with balance
pp Uni.accounts
```

(Source: [`run_ponzi_governmental.rb`](https://github.com/openblockchains/programming-crypto-contracts/blob/master/run_ponzi_governmental.rb))


resulting in:

```
[#<Account @address="0x1111", @balance=1000000>,
 #<Account @address="0xaaaa", @balance=1000000>,
 #<Account @address="0xbbbb", @balance=1000000>,
 #<Account @address="0xcccc", @balance=1000000>,
 #<Account @address="0xdddd", @balance=1000000>,
 #<Account @address="0xeeee", @balance=1000000>]
```

Let's start-up the contract with a 1 million seed jackpot!

``` ruby
## genesis - create contract
gov = Uni.send_transaction( from: '0x1111', value: 1_000_000, data: Governmental ).contract
pp gov
```

resulting in:

```
#<Governmental
   @balance=1000000,
   @buddies={},
   @corrupt_elite="0x1111",
   @creditor_addresses=[],
   @creditor_amounts=[],
   @last_creditor_paid_out=0,
   @last_time_of_new_credit=1548880299,
   @profit_from_crash=1000000,
   @round=0>
```

Now let's add the first four investments:

``` ruby
Uni.send_transaction( from: '0xaaaa', to: gov, value: 1_000_000 )
pp gov
Uni.send_transaction( from: '0xbbbb', to: gov, value: 1_000_000, data: [:lend_government_money, '0xaaaa'] )
pp gov
Uni.send_transaction( from: '0xcccc', to: gov, value: 1_000_000 )
pp gov
Uni.send_transaction( from: '0xdddd', to: gov, value: 1_000_000 )
pp gov

## (pp) pretty print all known accounts with balance
pp Uni.accounts
```

resulting in:

```
...
#<Governmental
    @balance=1450000,
    @buddies={"0xaaaa"=>0, "0xbbbb"=>0, "0xcccc"=>0, "0xdddd"=>1100000},
    @corrupt_elite="0x1111",
    @creditor_addresses=["0xaaaa", "0xbbbb", "0xcccc", "0xdddd"],
    @creditor_amounts=[1100000, 1100000, 1100000, 1100000],
    @last_creditor_paid_out=3,
    @last_time_of_new_credit=1548880299,
    @profit_from_crash=1200000,
    @round=0>
[#<Account @address="0x1111", @balance=200000>,
 #<Account @address="0xaaaa", @balance=1150000>,
 #<Account @address="0xbbbb", @balance=1100000>,
 #<Account @address="0xcccc", @balance=1100000>,
 #<Account @address="0xdddd", @balance=0>,
 #<Account @address="0xeeee", @balance=1000000>]
```

It's working!
The account `0xaaaa` invested 1_000_000 and got a payout of 1_150_000.
15_000 profit! 10% interest for the investment and a 5% bonus for the buddy referral fee.
The accounts `0xbbbb` and `0xcccc` got the "standard" payout of
1_100_000 for the 1_000_000 investment. 10% profit!
The last investor `0xdddd` is HODLing the bag and is all in
with a balance of 0 and waiting for the collapse / jackpot!


Let's move the blocktime ahead 13 hours :-) and mine a new block:

``` ruby
## mine - move block time ahead more than 12h (use 13h)
Uni.block = { number: 1, timestamp: Time.now.to_i + 60*60*13 }
```

Now let's send in a new investment
and let's see how the economy will collapse
and new (ponzi) round starts:

``` ruby
Uni.send_transaction( from: '0xeeee', to: gov, value: 1_000_000 )
pp gov

## (pp) pretty print all known accounts with balance
pp Uni.accounts
```

resulting in:

```
#<Governmental
    @balance=0,
    @buddies={"0xaaaa"=>0, "0xbbbb"=>0, "0xcccc"=>0, "0xdddd"=>1100000},
    @corrupt_elite="0x1111",
    @creditor_addresses=[],
    @creditor_amounts=[],
    @last_creditor_paid_out=0,
    @last_time_of_new_credit=1548927099,
    @profit_from_crash=0,
    @round=1>
[#<Account @address="0x1111", @balance=450000>,
 #<Account @address="0xaaaa", @balance=1150000>,
 #<Account @address="0xbbbb", @balance=1100000>,
 #<Account @address="0xcccc", @balance=1100000>,
 #<Account @address="0xdddd", @balance=1200000>,
 #<Account @address="0xeeee", @balance=1000000>]
```

Not bad. EVERYONE WINS! Except the corrupt elite -
only getting back 450_000 for the 1_000_000 seed investment.





## Find Out More

### References

- Home :: [github.com/s6ruby/universum](https://github.com/s6ruby/universum)
- Gem :: [universum](https://rubygems.org/gems/universum)
- Docs :: [universum](http://rubydoc.info/gems/universum)
- Article :: [Programming Crypto Blockchain Contracts Step-by-Step. Let's Start with Ponzi & Pyramid Schemes...](https://github.com/openblockchains/programming-crypto-contracts)
- Books :: [Best of Crypto Books](https://openblockchains.github.io/crypto-books/)


