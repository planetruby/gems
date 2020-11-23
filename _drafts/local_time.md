# Day XX - local_time gem - cache-friendly and timezone aware timestamp formatting

Written by [Matt Swanson](https://twitter.com/_swanson) from [Boring Rails](https://boringrails.com/)  {% avatar swanson %}

## What's wrong with Rails' `time_ago_in_words`?

Rails has several handy utilities baked into the framework for displaying relative dates and times.

You can use `time_ago_in_words` to convert timestamps into user-friendly strings like "4 hours ago", "2 days ago".

Or you could use `distance_of_time_in_words` to find out how much time has passed between two dates.

But there are two issues with using these options to display, for instance, when a record was last updated.

### Problem 1: Dealing with timezones

Ideally, you should show the timestamps to a user in their preferred timezone. Seeing that a `Post` was last updated at `10:30:20 UTC` might be fine for a computer, but humans don't like doing timezone conversions in their heads!

This is a tricky problem to solve with server-side code: are you going to store the timezone for each user? Pass it along on every request? And then remember to convert all your timestamps to the right timezone when you display it? Timezones are one of the most difficult things to get right when building software.

### Problem 2: Dealing with caching

Rails has a powerful caching system, but that system is based on computing hash signatures of the data being cached. If you do too much data transformation in your views, you might run into annoying and hard to debug caching issues in production.

Consider this simple example:

```ruby
# app/views/posts/index.html.erb

<% cache @posts do %>
  <% @posts.each do |post| %>
    <h2><%= post.title %></h2>
    <span>Updated: <%= time_ago_in_words(post.updated_at) %></span>
  <% end %>
<% end %>
```

Do you see the bug? It's tricky!

Rails uses the `@posts` collection as the cache key and will store off the rendered HTML. But we've got a problem. The cache is going to store the HTML fragment the first time it renders, so the value stored might be something like:

```html
<h2>My cool post!</h2>
<span>Updated: 4 hours ago</span>
```

But, wait! If you visit the page tomorrow, it's still going to show that the post was updated "4 hours ago" and not "yesterday" since none of the underlying data has changed to invalidate the cache.

## `local_time` to the rescue

You may be tempted to throw up your hands and turn off caching or switch the view back to printing out unfriendly UTC formatted time. But don't fret! There is a better solution from our friends at Basecamp.

The `local_time` gem is a small tool to solve this exact problem for Rails apps.

Instead of using `time_ago_in_words(date)`, install this gem and then use `local_time_ago(date)`. 

The gem also includes a small bit of JavaScript that can be used with either the asset pipeline or `webpacker` configurations.

```ruby
# app/views/posts/index.html.erb

<% cache @posts do %>
  <% @posts.each do |post| %>
    <h2><%= post.title %></h2>
    <span>Updated: <%= local_time_ago(post.updated_at) %></span>
  <% end %>
<% end %>
```

Now your app will render a `<time>` HTML tag that looks like this:

```html
<time datetime="2020-11-20T21:42:17Z" data-local="time-ago">November 20, 2020  9:42pm</time>
```

and then uses JavaScript to convert the time into the desired "time ago" format and the current timezone of the browser.

```html
<time datetime="2020-11-20T21:42:17Z" data-local="time-ago" title="November 20, 2020 at 4:42pm EST" aria-label="Friday">Friday</time>
```

Since the server-rendered response outputs only the UTC timestamp, the Rails caching will work as expected even as time passes or people in multiple timezones request the same content. And you get the additional benefit of your markup being more semantic (usage of the `<time>` tag), machine-readable (UTC format in the `datetime` attribute), and screen-reader friendly (automatic ARIA labels)!

This UI pattern has become a mainstay in nearly every web application and `local_time` is always one of the first gems I add to a Rails project.

**Note:** While this gem is old (released in 2013) and infrequently updated, don't be scared off thinking that this project is abandoned -- it's simply "done" and works great.

## Find Out More

### Hoe References

* home  :: [github.com/basecamp/local_time](https://github.com/basecamp/local_time)
* gem   :: [rubygems.org/gems/local_time](https://rubygems.org/gems/local_time)
* article :: [Basecamp's cache-friendly local time](https://signalvnoise.com/posts/3691-basecamps-cache-friendly-local-time)
