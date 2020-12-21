# Day 21 - super_diff Gem - A More Helpful Way to View Differences Between Complex Data Structures in Test Spec(ification)s


Written by {% avatar mcmire %} [Elliot Winkler](https://github.com/mcmire)

_A foodie, music nerd, aspiring polyglot, computer whisperer and full-stack web developer with years of experience at product companies and consultancies emphasizing in Ruby on Rails. Works with talented folks at [Tuft & Needle](https://www.tuftandneedle.com) selling great products for helping you sleep at night. Loves to learn new things and has a few different side projects going on at any given time._



## What is Super Diff?

Super Diff is a library that plugs into RSpec - test spec(ification)s in ruby - in order to give you more meaningful output when an expectation fails by providing a diff of the expected and actual data that is more complete than the one provided by RSpec.


## What's the problem?

Say we're building a Ruby class that's responsible for talking to an API:

``` ruby
require "twitter"

module MyCoolApp
  class TwitterClient
    def initialize
      @twitter = Twitter::REST::Client.new do |config|
        # ... configure Twitter accordingly ...
      end
    end

    def top_tweets_by(username)
      @twitter
        .user_timeline(
          username,
          count: 10,
          exclude_replies: true,
          include_rts: false
        )
        .map do |tweet|
          tweet.attrs.slice(:text, :entities, :lang, :created_at)
        end
    end
  end
end
```

As we happen to be using RSpec, we write a test that looks like this:

``` ruby
require "spec_helper"

describe MyCoolApp::TwitterClient, vcr: true do
  describe "#top_tweets_by" do
    it "returns the most recent 5 tweets of the given user" do
      client = described_class.new
      tweets = client.top_tweets_by("mcmire")
      expect(tweets).to eq([
        {
          text: "when something looks too good to be true.... https://t.co/68nswcjTda",
          entities: {
            hashtags: [],
            symbols: [],
            urls: [
              {
                display_url: "twitter.com/Schuldensuehne…",
                expanded_url: "https://twitter.com/Schuldensuehner/status/1333422680679337987",
                indices: [45, 68],
                url: "https://t.co/68nswcjTda"
              }
            ],
            user_mentions: []
          },
          lang: "en",
          created_at: "Mon Nov 30 18:14:51 +0000 2020",
        },
        {
          text: "i want to find new people to follow on YouTube but The Algorithm™ (as we all know) is horrible and only gives you p… https://t.co/NzYjbkwy23",
          entities: {
            hashtags: [],
            symbols: [],
            urls: [
              {
                display_url: "twitter.com/i/web/status/1…",
                expanded_url: "https://twitter.com/i/web/status/133346631915348608",
                indices: [117, 140],
                url: "https://t.co/NzYjbkwy23"
              }
            ],
            user_mentions: []
          },
          lang: "en",
          created_at: "Mon Nov 30 17:41:59 +0000 2020",
        },
        {
          text: "accidentally watered down coffee is the worst :(",
          entities: {
            hashtags: [],
            symbols: [],
            urls: [],
            user_mentions: []
          },
          lang: "en",
          created_at: "Mon Nov 30 17:02:14 +0000 2020",
        },
        {
          text: "really anxious to see if the batteries in the new macbooks last longer than this 😖 https://t.co/UI7HpbDClR",
          entities: {
            hashtags: [],
            media: [
              {
                display_url: "pic.twitter.com/UI7HpbDClR",
                expanded_url: "https://twitter.com/mcmire/status/1333144898787164160/photo/1",
                id: 1333143280444293120,
                id_str: "1333143280444293120",
                indices: [83, 106],
                media_url: "http://pbs.twimg.com/media/EoBGwBGUwAA5Nk2.jpg",
                media_url_https: "https://pbs.twimg.com/media/EoBGwBGUwAA5Nk2.jpg",
                sizes: {
                  large: { h: 502, resize: "fit", w: 1004 },
                  medium: { h: 502, resize: "fit", w: 1004 },
                  small: { h: 340, resize: "fit", w: 680 },
                  thumb: { h: 150, resize: "crop", w: 150 }
                },
                type: "photo",
                url: "https://t.co/UI7HpbDClR"
              }
            ],
            symbols: [],
            urls: [],
            user_mentions: []
          },
          lang: "en",
          created_at: "Sun Nov 29 21:24:47 +0000 2020",
        }
      ])
    end
  end
end
```

Now we go to run this test, and instead of receiving successful output, we get a failure:

```
MyCoolApp::TwitterClient
  #top_tweets_by
    returns the most recent 5 tweets of the given user (FAILED - 1)

Failures:

  1) MyCoolApp::TwitterClient#top_tweets_by returns the most recent 5 tweets of the given user
     Failure/Error:
       expect(tweets).to eq([
         {
           text: "when something looks too good to be true.... https://t.co/68nswcjTda",
           entities: {
             hashtags: [],
             symbols: [],
             urls: [
               {
                 display_url: "twitter.com/Schuldensuehne…",
                 expanded_url: "https://twitter.com/Schuldensuehner/status/1333422680679337987",

       expected: [{:created_at=>"Mon Nov 30 18:14:51 +0000 2020", :entities=>{:hashtags=>[], :symbols=>[], :urls=>[{:d...xious to see if the batteries in the new macbooks last longer than this 😖 https://t.co/UI7HpbDClR"}]
            got: [{:created_at=>"Mon Nov 30 18:14:51 +0000 2020", :entities=>{:hashtags=>[], :symbols=>[], :urls=>[{:d...xious to see if the batteries in the new macbooks last longer than this 😖 https://t.co/UI7HpbDClR"}]

       (compared using ==)

       Diff:
       @@ -18,7 +18,7 @@
            :symbols=>[],
            :urls=>
             [{:display_url=>"twitter.com/i/web/status/1…",
       -       :expanded_url=>"https://twitter.com/i/web/status/133346631915348608",
       +       :expanded_url=>"https://twitter.com/i/web/status/1333466319153348608",
               :indices=>[117, 140],
               :url=>"https://t.co/NzYjbkwy23"}],
            :user_mentions=>[]},
       @@ -29,7 +29,7 @@
          :entities=>{:hashtags=>[], :symbols=>[], :urls=>[], :user_mentions=>[]},
          :lang=>"en",
          :text=>"accidentally watered down coffee is the worst :("},
       - {:created_at=>"Sun Nov 29 21:24:47 +0000 2020",
       + {:created_at=>"Sun Nov 29 20:24:47 +0000 2020",
          :entities=>
           {:hashtags=>[],
            :media=>

     # ./spec/my_cool_app/twitter_client_spec.rb:8:in `block (3 levels) in <top (required)>'

Finished in 0.02713 seconds (files took 0.7756 seconds to load)
1 example, 1 failure

Failed examples:

rspec ./spec/my_cool_app/twitter_client_spec.rb:5 # MyCoolApp::TwitterClient#top_tweets_by returns the most recent 5 tweets of the given user

```

A brief glance tells us that both of these issues are typos in our test data: in the first one, a character is missing; in the second, the time is off by an hour. That's good, but _where_ in the test data do these typos occur?

That's not so easy to figure out. There seem to be line numbers listed here, but we will quickly realize that they are not very helpful, as they do not match the line numbers of our test file. If we are trained at interpreting RSpec's failure messages, we might look at the context surrounding these pairs of `-` and `+` lines and try searching for "133346631915348608" (or some piece of it) in the test file. That would help us fix the first typo, but we would not be so lucky with locating the second typo. We could certainly search for "21:24:47", but we would find that the order of the hash keys in the test output do not reflect the order of the hash keys in our test data. Eventually, of course, we would find it, but only after staring at the problem for some time.

## Enter Super Diff

Now let's try throwing in Super Diff, a new gem designed to highlight differences between complex data structures. We're using Rails in this scenario, so we'll add this line to our Gemfile under our `:test` group:

``` ruby
gem "super_diff"
```

Now we'll go to our `rails_helper.rb` (or open a file called `spec/support/super_diff.rb`, depending on how we've configured our project) and we'll add:

``` ruby
require "super_diff/rspec-rails"
```

Now when we run our tests, we get something like this:

```
MyCoolApp::TwitterClient
  #top_tweets_by
    returns the most recent 5 tweets of the given user (FAILED - 1)

Failures:

  1) MyCoolApp::TwitterClient#top_tweets_by returns the most recent 5 tweets of the given user
     Failure/Error:
       expect(tweets).to eq([
         {
           text: "when something looks too good to be true.... https://t.co/68nswcjTda",
           entities: {
             hashtags: [],
             symbols: [],
             urls: [
               {
                 display_url: "twitter.com/Schuldensuehne…",
                 expanded_url: "https://twitter.com/Schuldensuehner/status/1333422680679337987",

       Expected [{ text: "when something looks too good to be true.... https://t.co/68nswcjTda", entities: { hashtags: [], symbols: [], user_mentions: [], urls: [{ url: "https://t.co/68nswcjTda", expanded_url: "https://twitter.com/Schuldensuehner/status/1333422680679337987", display_url: "twitter.com/Schuldensuehne…", indices: [45, 68] }] }, lang: "en", created_at: "Mon Nov 30 18:14:51 +0000 2020" }, { text: "i want to find new people to follow on YouTube but The Algorithm™ (as we all know) is horrible and only gives you p… https://t.co/NzYjbkwy23", entities: { hashtags: [], symbols: [], user_mentions: [], urls: [{ url: "https://t.co/NzYjbkwy23", expanded_url: "https://twitter.com/i/web/status/1333466319153348608", display_url: "twitter.com/i/web/status/1…", indices: [117, 140] }] }, lang: "en", created_at: "Mon Nov 30 17:41:59 +0000 2020" }, { text: "accidentally watered down coffee is the worst :(", entities: { hashtags: [], symbols: [], user_mentions: [], urls: [] }, lang: "en", created_at: "Mon Nov 30 17:02:14 +0000 2020" }, { text: "really anxious to see if the batteries in the new macbooks last longer than this 😖 https://t.co/UI7HpbDClR", entities: { hashtags: [], symbols: [], user_mentions: [], urls: [], media: [{ id: 1333143280444293120, id_str: "1333143280444293120", indices: [83, 106], media_url: "http://pbs.twimg.com/media/EoBGwBGUwAA5Nk2.jpg", media_url_https: "https://pbs.twimg.com/media/EoBGwBGUwAA5Nk2.jpg", url: "https://t.co/UI7HpbDClR", display_url: "pic.twitter.com/UI7HpbDClR", expanded_url: "https://twitter.com/mcmire/status/1333144898787164160/photo/1", type: "photo", sizes: { thumb: { w: 150, h: 150, resize: "crop" }, medium: { w: 1004, h: 502, resize: "fit" }, small: { w: 680, h: 340, resize: "fit" }, large: { w: 1004, h: 502, resize: "fit" } } }] }, lang: "en", created_at: "Sun Nov 29 20:24:47 +0000 2020" }]
          to eq [{ text: "when something looks too good to be true.... https://t.co/68nswcjTda", entities: { hashtags: [], symbols: [], urls: [{ display_url: "twitter.com/Schuldensuehne…", expanded_url: "https://twitter.com/Schuldensuehner/status/1333422680679337987", indices: [45, 68], url: "https://t.co/68nswcjTda" }], user_mentions: [] }, lang: "en", created_at: "Mon Nov 30 18:14:51 +0000 2020" }, { text: "i want to find new people to follow on YouTube but The Algorithm™ (as we all know) is horrible and only gives you p… https://t.co/NzYjbkwy23", entities: { hashtags: [], symbols: [], urls: [{ display_url: "twitter.com/i/web/status/1…", expanded_url: "https://twitter.com/i/web/status/133346631915348608", indices: [117, 140], url: "https://t.co/NzYjbkwy23" }], user_mentions: [] }, lang: "en", created_at: "Mon Nov 30 17:41:59 +0000 2020" }, { text: "accidentally watered down coffee is the worst :(", entities: { hashtags: [], symbols: [], urls: [], user_mentions: [] }, lang: "en", created_at: "Mon Nov 30 17:02:14 +0000 2020" }, { text: "really anxious to see if the batteries in the new macbooks last longer than this 😖 https://t.co/UI7HpbDClR", entities: { hashtags: [], media: [{ display_url: "pic.twitter.com/UI7HpbDClR", expanded_url: "https://twitter.com/mcmire/status/1333144898787164160/photo/1", id: 1333143280444293120, id_str: "1333143280444293120", indices: [83, 106], media_url: "http://pbs.twimg.com/media/EoBGwBGUwAA5Nk2.jpg", media_url_https: "https://pbs.twimg.com/media/EoBGwBGUwAA5Nk2.jpg", sizes: { large: { h: 502, resize: "fit", w: 1004 }, medium: { h: 502, resize: "fit", w: 1004 }, small: { h: 340, resize: "fit", w: 680 }, thumb: { h: 150, resize: "crop", w: 150 } }, type: "photo", url: "https://t.co/UI7HpbDClR" }], symbols: [], urls: [], user_mentions: [] }, lang: "en", created_at: "Sun Nov 29 21:24:47 +0000 2020" }]

       Diff:

       ┌ (Key) ──────────────────────────┐
       │ ‹-› in expected, not in actual  │
       │ ‹+› in actual, not in expected  │
       │ ‹ › in both expected and actual │
       └─────────────────────────────────┘

         [
           {
             text: "when something looks too good to be true.... https://t.co/68nswcjTda",
             entities: {
               hashtags: [],
               symbols: [],
               user_mentions: [],
               urls: [
                 {
                   url: "https://t.co/68nswcjTda",
                   expanded_url: "https://twitter.com/Schuldensuehner/status/1333422680679337987",
                   display_url: "twitter.com/Schuldensuehne…",
                   indices: [
                     45,
                     68
                   ]
                 }
               ]
             },
             lang: "en",
             created_at: "Mon Nov 30 18:14:51 +0000 2020"
           },
           {
             text: "i want to find new people to follow on YouTube but The Algorithm™ (as we all know) is horrible and only gives you p… https://t.co/NzYjbkwy23",
             entities: {
               hashtags: [],
               symbols: [],
               user_mentions: [],
               urls: [
                 {
                   url: "https://t.co/NzYjbkwy23",
       -           expanded_url: "https://twitter.com/i/web/status/133346631915348608",
       +           expanded_url: "https://twitter.com/i/web/status/1333466319153348608",
                   display_url: "twitter.com/i/web/status/1…",
                   indices: [
                     117,
                     140
                   ]
                 }
               ],
             },
             lang: "en",
             created_at: "Mon Nov 30 17:41:59 +0000 2020"
           },
           {
             text: "accidentally watered down coffee is the worst :(",
             entities: {
               hashtags: [],
               symbols: [],
               user_mentions: [],
               urls: []
             },
             lang: "en",
             created_at: "Mon Nov 30 17:02:14 +0000 2020"
           },
           {
             text: "really anxious to see if the batteries in the new macbooks last longer than this 😖 https://t.co/UI7HpbDClR",
             entities: {
               hashtags: [],
               symbols: [],
               user_mentions: [],
               urls: [],
               media: [
                 {
                   id: 1333143280444293120,
                   id_str: "1333143280444293120",
                   indices: [
                     83,
                     106
                   ],
                   media_url: "http://pbs.twimg.com/media/EoBGwBGUwAA5Nk2.jpg",
                   media_url_https: "https://pbs.twimg.com/media/EoBGwBGUwAA5Nk2.jpg",
                   url: "https://t.co/UI7HpbDClR",
                   display_url: "pic.twitter.com/UI7HpbDClR",
                   expanded_url: "https://twitter.com/mcmire/status/1333144898787164160/photo/1",
                   type: "photo",
                   sizes: {
                     thumb: {
                       w: 150,
                       h: 150,
                       resize: "crop"
                     },
                     medium: {
                       w: 1004,
                       h: 502,
                       resize: "fit"
                     },
                     small: {
                       w: 680,
                       h: 340,
                       resize: "fit"
                     },
                     large: {
                       w: 1004,
                       h: 502,
                       resize: "fit"
                     }
                   }
                 }
               ]
             },
             lang: "en",
       -     created_at: "Sun Nov 29 21:24:47 +0000 2020"
       +     created_at: "Sun Nov 29 20:24:47 +0000 2020"
           }
         ]
     # ./spec/my_cool_app/twitter_client_spec.rb:8:in `block (3 levels) in <top (required)>'

Finished in 0.05311 seconds (files took 0.79852 seconds to load)
1 example, 1 failure

Failed examples:

rspec ./spec/my_cool_app/twitter_client_spec.rb:5 # MyCoolApp::TwitterClient#top_tweets_by returns the most recent 5 tweets of the given user
```

While the output is longer, it is more comprehensive. In a matter of seconds, we have a clear picture of what was changed where, and we can use this knowledge to modify our test data accordingly.

## Super Diff is intelligent

Admittedly, the example above was relatively simple, as our test data only involved hashes, arrays, strings, and numbers. However, Super Diff also knows how to compare Active Record objects, `HashWithDifferentAccess` objects, "placeholder" objects such as `an_object_having_attributes` or `a_hash_including`, and more. This means that we can replace tests like this:

``` ruby
created_posts = Post.all
expect(created_posts.first.title).to eq("My First Post")
expect(created_posts.first.content).to eq("Some cool post")
expect(created_posts.second.title).to eq("My Second Post")
expect(created_posts.second.content).to eq("Another cool post")
```

with something like this:

``` ruby
expect(Post.all).to match_array([
  an_object_having_attributes(
    title: "My First Post",
    content: "Some cool post"
  ),
  an_object_having_attributes(
    title: "My Second Post",
    content: "Another cool post"
  )
])
```

...and - if such a test fails - get useful output that looks and feels similar to our test data.

## Super Diff is not just for Rails

You might think that this gem can only be used for Rails. Not so! This happens to be the most popular use case, but you can also use it in any Ruby project. Instead of having this in your `spec_helper`:

``` ruby
require "super_diff/rspec-rails"
```

you'd say this:

``` ruby
require "super_diff/rspec"
```

## Find Out More

See the [README](https://github.com/mcmire/super_diff/blob/master/README.md#installation) for other ways that you can use the gem to suit your needs.

Happy testing!

### References

- Home :: [github.com/mcmire/super_diff](https://github.com/mcmire/super_diff)
- Gem :: [super_diff](https://rubygems.org/gems/super_diff)
