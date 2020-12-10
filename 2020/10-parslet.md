# Day 10 - parslet Gem - Build Your Own Language Parsers (and Lexers) with Parsing Expression Grammars (PEGs)


Written by {% avatar rlgreen91 %} [Rachel Green](https://github.com/rlgreen91)

_A web developer based in Houston, Texas with experience in building business-to-business (B2B), e-commerce, and enterprise applications. Admin for the [Ruby on Rails Slack group](https://www.rubyonrails.link/) and also involved with civic engagement and advocacy efforts and passionate about the potential for tech to do good for others._



Welcome to our exploration of building parsers in Ruby!  How do you do that, you ask?  Well, using the gem Parslet!

[Parslet](http://kschiess.github.io/parslet/) is a gem that allows developers to create parsers for string inputs using parsing expression grammar, or PEG.  This is just the academic way of saying that we create [a set of rules for recognizing strings in a formal language](https://en.wikipedia.org/wiki/Parsing_expression_grammar).  Those rules use regular expressions to determine whether an input string behaves the way we expect, and provides us with an output object that we can later manipulate.

In the rest of this article, we'll create a simple version of a parser using Parslet.  To keep things light, this tutorial will focus only on creating the parser based on a single question written in English using the Student Questions for Purposeful Reading
(SQPR) format for mathematics text - a simple version of a parser I created during an internship many years ago.

Typically, using Parslet does rely heavily on a good foundation in regular expressions, or regex.  Since we're focused on the design aspect of the parser, I'll provide the necessary regexes along with a brief explanation of what each one does.  No prior knowledge of regex is required to follow along.

Similarly, the parser doesn't really care or know what the input data you give it means at a deep level, and for this tutorial, we as the programmer can take that same approach.  While there are places where we'll want to rely on details specific to SQPR, I'll provide those like the regexes - you won't need to know that to follow along.

All of our code will be written within a single Ruby file.  When we run our script via a terminal, we'll print our output there.  Really, all you'll need for this tutorial is a basic understanding of how written English works.  So with that, let's get started!

## Set Up
Let's set our environment up.  In the tutorial, I'll be using Ruby 2.7.  Parslet is compatible with Ruby versions >= 1.9, so if you have an older version of Ruby that works, feel free to use that.  Just remember that my syntax may differ from yours in places.

If you don't have a compatible version of Ruby installed, head over to [ruby-lang.org](https://www.ruby-lang.org/en/) or your favorite version manager to install 2.7.

Next, create a file named `parslet_tutorial.rb` and add the following code to it:

``` ruby
require 'bundler/inline'

gemfile do
  source 'https://rubygems.org'
  gem 'parslet'
end

class SPQRParser < Parslet::Parser
end

# p SPQRParser.new.parse("<![CDATA[")
# p SPQRParser.new.parse("<![CDATA[The meaning of ")
# p SPQRParser.new.parse("<![CDATA[The meaning of \"negative frequency\"")
# p SPQRParser.new.parse("<![CDATA[The meaning of \"negative frequency\" in a Fourier series is:")
# p SPQRParser.new.parse("<![CDATA[The meaning of \"negative frequency\" in a Fourier series is:]]>")
```

Here we use Bundler to install the Parslet gem so we can use its methods.  At the end of the file, we have a set of commands where we call our parser on a provided input and print the results to the terminal.  If you look at lines 11-15, you'll see that we gradually work our way up to the full sample question, which is `"<![CDATA[The meaning of "negative frequency" in a Fourier series is:]]>"`.  This, when parsed should be:

```
The meaning of "negative frequency" in a Fourier series is:
```

Now we can go ahead and create our parser by putting some stuff in that empty class!

## The First Input - Early Success!
Let's take a look at our first input on line 11.  Reading out of order, we see that we create a new instance of our SPQRParser class and call the instance method `parse` on the provided string, the result of which is printed to the terminal.  Our first input is `"<![CDATA["`.

As I explained in the intro, Parslet allows you to create parsers using PEG, or a set of rules to describe the expected input.  This is something we do naturally as we learn a language.  Think back to when you first learned English - you learned how to conjugate verbs, or change them to match the subject, in the following way:

- I run --> You run --> He/She runs
- I walk --> You walk --> He/She walks
- I bake --> You bake --> He/She bakes

Regardless of *how* you learned English, at some point you probably realized, consciously or unconsciously, the following rule:

Given a verb that is [to blank], you conjugate it as:
I [blank] --> You [blank] --> He/She [blank + "s"]

This is a good rule that works most of the time when it comes to conjugating verbs, right?  Sure, there's some exceptions, but you learn them as you go.  Creating a PEG parser works in the same manner, where we
determine the rules of the text we want to parse and tell it how to handle inputs based on those rules.

Let's update our `SPQRParser` class with the following code:

``` ruby
class SPQRParser < Parslet::Parser
  rule(:open_tag)   { str("<!\[CDATA\[") }
  rule(:expression) { open_tag }

  root :expression
end
```

Here we created two rules and a root.  Our first rule, `open_tag`, says to match the string `"<!\[CDATA\["` exactly.  `"<![CDATA["` is the opening tag used in SPQR to indicate that the following text is written in that format.  Additionally, brackets have a special meaning in regex, so we use a backslash to escape the character, meaning we want to match that character.

Our second rule, `expression` just contains `open_tag`.  In Parslet, you can "call" other rules inside of rules.  This adds an element of recursion to our parsing, where Ruby will parse that called rule as part of evaluating another rule.  However, we want to be careful that our recursion doesn't lead to an endless loop.  So, it's best to think of a rule that describes an input and then determine how we can put them together via recursion.

We also created a root, which tells Parslet what rule to start with when it begins parsing an input - in this case, we start with `expression`.  Altogether, we start with `expression`, which tells Parslet to look for `open_tag`, which says to match `"<![CDATA["` exactly.

Let's uncomment our first command, which is now on line 15.  We can then run our file using the following command:

```
$ ruby parslet_tutorial.rb
```

This will evaluate evaluate line 15, and we should get the following output:

```
<![CDATA[@0
```

Success! We see the string the parser successfully parsed, followed by the line number where the input started (in this case, 0). Ok, let's move on to the next one.

## The Second Input - When Does a Word End?
We'll comment out line 15 again and uncomment line 16.  When we run the file again, we run into our first error:

```
Don't know what to do with "The meanin" at line 1 char 10. (Parslet::ParseFailed)
```

This is pretty straightforward - the parser gets through `<![CDATA[` just fine.  However, once it hits `The mea...` it gets a bit lost, and like the message says, it doesn't know what to do.

At first glance, this might seem to have a trivial solution.  We need to tell the parser how to deal with regular text.  And we'll do that in a second.  But let's consider another, perhaps more subtle detail: when is the parser done with an input?

Now, we could say that the parser is done when the input string ends - let's try that out.

If I write `"A sentence has a subject and a"` in English, would you consider that to be "done?" After all, the second `a` marks the end of the string.  No, most of you would say that is an incomplete sentence and therefore not "done".  Like in our verb conjugation example above, at some point we absorbed the concept of a "sentence" in English and a handful of associated rules.  The most basic one says that a sentence should end in a punctuation mark like `., ? or !` and that a punctuation marker is a reliable marker for the end of a sentence.  As we go along, we'll define these rules for ending something in multiple places in our parser, from determining the ending of text to math equations to the expression itself.

For now, let's return to adding the ability to recognize text.  Update your parser to have the following code:

``` ruby
  rule(:letters)    { match(/\w/).repeat(1) }

  rule(:space)      { match("\s").repeat(1) }

  #Grammar parts
  rule(:open_tag)   { str("<!\[CDATA\[") }
  rule(:text)       { (letters >> space ).repeat(1) }

  rule(:expression) { open_tag >> text }
  root :expression
```

Let's start with the new rules we've added.  The first new rule is `letters`, where we match all alphabetic characters, both uppercase and lowercase. The `repeat(1)` at the end signifies that the preceding regex occurs at least one but can repeat an infinite number of times.

Remember our brief discussion about how we know if something is "done"?  Well, another rule we absorbed from English is that the end of a word, or its last letter, is followed by a space.  So we also add a rule called `space` to catch that using the special character `\s`.  This will match whitespace in general, which includes a space from the spacebar, spaces from the tab key, and more.  The `repeat(1)` tells us again that this occurs at least once but can repeat an infinite number of times - so we can catch people who use one space after a sentence *and* people who use two spaces.

Our final new rule is `text`, which puts the above rules together.  Text is recognized by `letters` followed by `space`, and occurs at least once but can repeat an infinite number of times.  We then update `expression` to look for the `open_tag` followed by `text`.  Run the program again and you should see the following:

```
"<![CDATA[The meaning of "@0
```

We're doing well!  Let's move on to the next input.

## The Third Input: Handling Special Characters
Let's comment out our second input (which has moved to line 22 due to our earlier changes) and uncomment the next one on line 23.  Running the program again, we see the following error:

```
Failed to match sequence (OPEN_TAG TEXT) at line 1 char 10. (Parslet::ParseFailed)
```

Let's go through the input character by character.  We started out by detecting the open tag again.  This time, we did detect the text, which is at least one letter followed by one space.  If you count to line 1 character 10, you'll see that Parslet raises an error when it encounters `\"`.  As a note, `"` is used to denote the beginning and end of a string, and so when the character is literally used within a string, we should expect it to be preceded by a backslash to escape it - another one of those special characters.

This, fortunately, can be defined using a simple rule, which we'll add as shown below.  The code below also contains another new rule that we'll cover shortly.

``` ruby
  rule(:letters)    { match(/\w/).repeat(1) }

  rule(:space)      { match("\s").repeat(1) }

  rule(:quote)      { str("\"") || str("\'") }
  rule(:quote?)     { quote.maybe }

  #Grammar parts
  rule(:open_tag)   { str("<!\[CDATA\[") }
  rule(:text)       { (quote? >> letters >> ( space | quote )).repeat(1) }

  rule(:expression) { open_tag >> text }
  root :expression
```

Our first new rule, `quote` allows us to exactly match a quotation mark, whether it's a single quotation mark (') or a double quotation mark (").  Let's walk through our decision on how we can modify the `text` rule to account for this new character.

Before, we defined the `text` rule as finding a sequence of `letters` followed by `space` at least once.  If we wanted to focus specifically on our example input, then we could just say that now, `text` is finding a sequence of `letters` followed by `space` and then followed by `quote`.  But if we think back to our English lessons, we remember that if we have a group of words, a quotation mark can occur:
- at the beginning of the group (meaning directly before the words)
- in the middle of the group (meaning after some of the words and before some of the other words), or
- at the end of the group (meaning directly after the words)

Let's tackle a quotation mark that's at the beginning of a group of words.  First, we create the rule `quote?` which uses the `maybe` method to indicate that `quote`, well, may or may not be found.  We can then update `text` as shown to include `quote?` at the beginning of the sequence.  Now it should match if a quotation mark is there or not.

Ok, so what if a quote is in the middle of a group of words, like in our example input?  Luckily, our `text` rule mostly accounts for that with no changes on our part.  Let's step through our example input:

`The ` -> matches as `letters` followed by `space`, and the rule says that there may be another matching sequence present
`meaning ` -> matches as `letters` followed by `space`, and the rule says that there may be another matching sequence present
`of ` -> matches as `letters` followed by `space`, and the rule says that there may be another matching sequence present
`\"negative ` -> matches as `quote` followed by `letters` followed by `space`, and the rule says that there may be another matching sequence present

The only thing that presents a problem is `frequency\"`, and that's really an exception to a decision we made earlier.  Remember, we originally said that we know a word is "done" because the letters are followed by a space.  Except, that's not always true - the letters can be followed by a punctuation mark at the end of a sentence, like we said above.  And in this particular case, we know that a group of words, which is a collection of letters and spaces, can be directly followed a quotation mark.  So, to accommodate this, we update `text` to end with either `space` or `quote` - but, it must end with one or the other.

Run the program again and you should see the following successful output:

```
"<![CDATA[The meaning of \"negative frequency\""@0
```

We're making pretty good progress!  Let's move on to the next input.

## The Fourth Input: Rethinking an Earlier Decision
Let's comment out our last example input (which has moved to line 26 due to our changes) and uncomment the next one on line 27.  When we run the program we run into the same error as above, where our input doesn't match the sequence of `open_tag` followed by `text` defined in `expression`.

It turns out that we forgot to account for when a quotation mark is in the middle of a group of words - it will be after some of the words, and then directly before a space and then some of the other words.  So at this point, we need to modify `text` to indicate that it is `quote?` (so a quotation mark may be there) followed by `letters` followed by `space` or `quote`, and if it is followed by `quote` then that is followed by `space`, and you should find this at least once.

Yeah, that hurt my head just typing that out.  Let's take a step back and think about what we know about quotes in English.  One of the main reasons we use quotation marks is to mark a specific word or term, which is itself a group of words.  In the example input, we have the term "negative frequency".  In general, a term is going to start with a quotation mark, followed by words, followed by a quotation mark.  Instead of trying for the right combo of quotation marks, letters, and spaces to define `text`, let's reframe it in the context of terms and words.  Update your parser with the code below:

``` ruby
  rule(:letters)    { match(/\w/).repeat(1) }

  rule(:space)      { match("\s").repeat(1) }
  rule(:space?)     { space.maybe }

  rule(:quote)      { str("\"") || str("\'") }

  #Grammar parts
  rule(:open_tag)   { str("<!\[CDATA\[") }
  rule(:word)       { letters >> space? }
  rule(:term)       { quote >> word.repeat(1) >> quote >> space? }
  rule(:text)       { (term | word).repeat(1) }

  rule(:expression) { open_tag >> text }
  root :expression
```

First, we add the `space?` rule to account for times where a space may or may not be present.  We can then define the `word` rule to be the sequence of `letters` followed by `space?`. Next, we add `term` which looks for `quote` followed by `word`, which occurs at least once, followed by `quote` and then followed by `space?`.  This allows us to catch cases where:
- `term` is at the beginning of a group of words, or
- in the middle of a group of words and would be followed by a space before the next word, or
- at the end of a group of words and not followed by a space.

And since `word` ends with `space?`, we can guarantee that `term` accommodates the case where a quotation mark directly follows a letter.

Now, a term like "negative frequency" can occur at the beginning of a group of words, in the middle of a group of words, or at the end of a group of words, like the original issue with quotation marks.  But since we've made `term` and `word` their own separate rules, it's easier to specify the desired input in `text` as shown above.

`text` is either a `term` or a `word`, and that will occur at least once.  This allows us to account for a term at the beginning of a group of words, in the middle of a group of words, or at the end of a group of words.  It also accounts for inputs where a term is by itself or there's one or more words with no term at all.  And, as a result of this new approach, we can drop the now unnecessary rule `quote?`.

Now, if you try to run the program, you'll still run into that error.  A quick perusal of our input shows one last thing: a `:` at the end of our string.  We need our parser to recognize when letters are followed not by a space, but rather a punctuation mark. To do so, update the parser with the following changes:

``` ruby
  rule(:letters)    { match(/\w/).repeat(1) }

  rule(:space)      { match("\s").repeat(1) }
  rule(:space?)     { space.maybe }

  rule(:quote)      { str("\"") || str("\'") }

  rule(:colon)      { str(":") }
  rule(:colon?)     { colon.maybe }

  #Grammar parts
  rule(:open_tag)   { str("<!\[CDATA\[") }
  rule(:word)       { letters >> colon? >> space? }
  rule(:term)       { quote >> word.repeat(1) >> quote >> space? }
  rule(:text)       { (term | word).repeat(1) }

  rule(:expression) { open_tag >> text }
  root :expression
```

Here, we do the same thing we did with spaces earlier, where we add `colon` to match the character directly and `colon?` to indicate that `colon` may or may not occur.  In fact, the only difference is that while `space` accounts for repeated characters, `colon` does not.  Then we update `word` to indicate that it consists of letters that may be followed by `colon` which may be followed by `space`.

Running the program again, we should see the following successful output:

```
"<![CDATA[The meaning of \"negative frequency\" in a Fourier series is:"@0
```

We're about to cross the finish line!  Let's move on to our final and full example input.


## The Final ~~Frontier~~ Input
We can comment out our previous input (now at line 32 due to our changes) and uncomment our final input at line 33:

```
"<![CDATA[The meaning of \"negative frequency\" in a Fourier series is:]]>"
```

This one I'll give to you - as `"<!\[CDATA\["` is our opening tag, `\]\]>` is our closing tag.  We can add one last rule to get our completed version of the parser below:

``` ruby
  rule(:letters) { match(/\w/).repeat(1) }

  rule(:space) { match("\s").repeat(1) }
  rule(:space?) { space.maybe }

  rule(:quote) { str("\"") || str("\'") }

  rule(:colon) { str(":") }
  rule(:colon?) { colon.maybe }

  #Grammar parts
  rule(:open_tag) { str("<!\[CDATA\[") }
  rule(:close_tag) { str("\]\]>") }
  rule(:word) { letters >> colon? >> space? }
  rule(:term) { quote >> word.repeat(1) >> quote >> space? }
  rule(:text) { (term | word).repeat(1) }

  rule(:expression) { open_tag >> text >> close_tag }
  root :expression
```

We add our `close_tag` rule to determine the closing tag, and then update `expression` to look for a sequence of `open_tag` followed by `text` followed by `close_tag`.  Running the program one last time, we should get the following successful output:

```
"<![CDATA[The meaning of \"negative frequency\" in a Fourier series is:]]>"@0
```

The good news is that it is successful!  Unless you have a typo somewhere, there should be no errors, and the program should print the results of the parser to the terminal.  The bad news is that, as currently written, the results of the parser are...the same exact string that we gave it as input?

This is actually by design. Parslet tries its best to stay out of your way.  Accordingly, it doesn't tell you what to do with your input or what it means - it just repeats it back to you.  You have to specify the structure of the parser output.  So let's hop to it in the next section.

## Adding Structure to Our Output - What's in a Name?
You can specify the structure of the parser output using `.as(:symbol)`.

Let's update the `word` rule as shown below:

``` ruby
rule(:word) { (letters >> colon? >> space?).as(:word) }
```

This will instruct the parser to recognize and assign any matches for `word` as `word`.  Now, the symbol you use in `.as(:symbol)` doesn't have to match the rule name, it just happens to this case.  But in general, we tell it to recognize a successful match of a rule to a name.  Let's run the program again and see what happens:

```
[{:word=>"The "@9}, {:word=>"meaning "@13}, {:word=>"of "@21}, {:word=>"in "@45},
 {:word=>"a "@48}, {:word=>"Fourier "@50}, {:word=>"series "@58}, {:word=>"is:"@65}]
```

Now, we get a tree structure that shows us the matching input marked as `word` and the corresponding column number of the beginning of the match.  In particular, each match is a hash, with `:word` as the key and the matching string and starting column number as the value.  The hash itself is referred to as a subtree, with the value or *accepted source* simply referred to as a string.  These hashes are elements in an array.  An array of hashes is used to indicate a collection of subtrees that occurs via reptition.  If we look back at our parser, we'll see that `word` is repeated in multiple places, so Parslet returns an array containing each applicable subtree for `:word`.

This is a good start, but, as you see, we only get back inputs marked as `:word`.  Again, Parslet is trying its best to stay out of our way.  Once you use `.as(:symbol)` in one rule, it then assumes that anything not named using `.as(:symbol)` is unimportant.  And, if it's unimportant, it's not worth including the output, which is printed to the terminal here.  So it's up to us to decide what is important and mark it appropriately.

Let's think: well, in this scenario, the opening and closing tags aren't that important, so we don't need to mark them.  It's great that we marked `:word`, but what happened to our term "negative frequency?"  Let's update that rule as shown below:

``` ruby
rule(:term) { (quote >> word.repeat(1) >> quote >> space?).as(:term) }
```

Now, we re-run the program:

```
[{:word=>"The "@9}, {:word=>"meaning "@13}, {:word=>"of "@21},
 {:term=>[{:word=>"negative "@25}, {:word=>"frequency"@34}]}, {:word=>"in "@45},
 {:word=>"a "@48}, {:word=>"Fourier "@50}, {:word=>"series "@58}, {:word=>"is:"@65}]
```

There it is now.  Our tree now includes all of the words and terms.  A `:term` itself is a subtree, with `:term` as the key and the value an array of words.  In general, when you have a named rule, Parslet will create an output where:
- the match is a subtree, represented as a hash, with a key that is the name, and a value that is the matching input and location
- an array to indicate multiple occurrences of a named rule, represented as subtrees
- an array to indicate a sequence of named rules

That last point is key - remember, Parslet gives us the ability to add recursion to our parser by calling rules within rules.  Well, once we name those rules, this same pattern of recursion occurs in the output.  We can have a subtree for a named rule where the key of the hash is the name, and the value is whatever the output of the called named rule.  This is why currently, `:term` is a subtree represented by an array of `:word` subtrees, since a term can have multiple words.

Ok, so overall this is closer.  But we still don't have the actual quotation marks we need to distinguish a term from words.  Let's update the `quote` rule to name those:

``` ruby
rule(:quote) { (str("\"") || str("\'")).as(:quote) }
```

Re-running the program we get:

```
[{:word=>"The "@9}, {:word=>"meaning "@13}, {:word=>"of "@21},
 {:term=>[{:quote=>"\""@24}, {:word=>"negative "@25}, {:word=>"frequency"@34},
 {:quote=>"\""@43}]}, {:word=>"in "@45}, {:word=>"a "@48}, {:word=>"Fourier "@50},
 {:word=>"series "@58}, {:word=>"is:"@65}]
```

Now, our tree includes all of the words and terms, which themselves are broken down into quotation marks and words.  Given our input, we have parsed and captured all of the important data.

## What's Next
You're probably wondering what comes next after we generate the tree structure.  At this point, you can choose to work directly with the tree structure to do whatever you want.  But as your parser grows in complexity, so would the corresponding tree structure, so this can be hard.

Because of this, Parslet also provides a class called `Parslet::Transform`, which allows you to create your own hash transformation engine.  Just like `Parslet::Parser` allows us to specify rules in our parser for how to parse an input string, `Parslet::Transform` allows us to specify rules for how to handle the different part of the output tree.  You can write rules that will match the "strings" I noted above, subtrees, or even the whole tree altogether.  The rules can be used to do pretty much anything: change one string to another, like calling `.uppercase()`, convert a string to an integer, evaluate an expression, really, the sky is the limit.

Our example input, even in its full form, is pretty basic, so I won't bother going through a transformer implmentation here.  Still, if you'd like to see an example of what a more robust parser and transformer would look like, key an eye out for Part 2 to appear later this month.

Well, that's it for now!  If you're interested, here's the final version of our code:

``` ruby
require 'bundler/inline'

gemfile do
  source 'https://rubygems.org'
  gem 'parslet'
end

class SPQRParser < Parslet::Parser
  rule(:letters) { match(/\w/).repeat(1) }

  rule(:space) { match("\s").repeat(1) }
  rule(:space?) { space.maybe }

  rule(:quote) { (str("\"") || str("\'")).as(:quote) }

  rule(:colon) { str(":") }
  rule(:colon?) { colon.maybe }

  #Grammar parts
  rule(:open_tag) { str("<!\[CDATA\[") }
  rule(:close_tag) { str("\]\]>") }
  rule(:word) { (letters >> colon? >> space?).as(:word) }
  rule(:term) { (quote >> word.repeat(1) >> quote >> space?).as(:term) }
  rule(:text) { (term | word).repeat(1) }

  rule(:expression) { open_tag >> text >> close_tag }
  root :expression
end

# p SPQRParser.new.parse("<![CDATA[")
# p SPQRParser.new.parse("<![CDATA[The meaning of ")
# p SPQRParser.new.parse("<![CDATA[The meaning of \"negative frequency\"")
# p SPQRParser.new.parse("<![CDATA[The meaning of \"negative frequency\" in a Fourier series is:")
p SPQRParser.new.parse("<![CDATA[The meaning of \"negative frequency\" in a Fourier series is:]]>")
```

Thanks for reading along, and I hope you enjoyed
our dive into creating parsers with parslet!




## Find Out More

### References

- Home :: [kschiess.github.io/parslet](http://kschiess.github.io/parslet/)
- Source :: [github.com/kschiess/parslet](https://github.com/kschiess/parslet)
- Gem :: [rubygems.org/gems/parslet](https://rubygems.org/gems/parslet)
- Docs :: [rubydoc.info/gems/parslet](https://rubydoc.info/gems/parslet)


