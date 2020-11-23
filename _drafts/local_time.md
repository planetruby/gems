# Day XX - local_time gem - cache-friendly and timezone aware formatting for timestamps

Written by [Matt Swanson](https://twitter.com/_swanson)  {% avatar swanson %}

## What's wrong with Rails' `time_ago_in_words`?

Rails has several handy utilities baked into the framework for displaying relative times.

You can use `time_ago_in_words` to convert timestamps into user-friendly strings like "4 hours ago", "2 days ago".

Or you could use `distance_of_time_in_words` to find out how much time has passed between two dates.

But there are two issues with using these options when you are trying to display, for instance, when a record was last updated.

### Problem 1: Dealing with timezones

Ideally, you could show the timestamps to a user in their preferred timezone. See that a `Post` was last updated at `10:30:20 UTC` might be fine for a computer, but humans typically don't like doing timezone conversions in their heads!

This can be a tricky problem to solve using server-side code: are you going to store the timezone for each user? Pass it along on every request? And then remember to convert all your timestamps to the right timezone when you display it?

### Problem 2: Dealing with caching

Rails has a powerful caching system, but that system is based on computing hash signatures of the data being cache. If you do too much transformation of the data in your views, you might run into annoying and hard to debug caching issues in production.

Consider this subtle caching bug:

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

## So what can we do?

You may be tempted to throw up your hands and turn off caching or switch the view back to printing out unfriendly UTC formatted time. But don't fret! There is a better solution from our friends at Basecamp.

The `local_time` gem is a small tool to solve this exact problem for Rails apps.

Instead of using `time_ago_in_words`, install this gem and then use `local_time_ago`.

The gem will render a `<time>` HTML tag that looks something like this:

```html
<time datetime="2020-11-20T21:42:17Z" data-local="time-ago">November 20, 2020  9:42pm</time>
```

and then uses JavaScript to convert the time into the desired "time ago" format into the current timezone of the browser.

```html
<time datetime="2020-11-20T21:42:17Z" data-local="time-ago" title="November 20, 2020 at 4:42pm EST" aria-label="Friday">Friday</time>
```

Since the server-rendered response uses the UTC timestamp, it will also return the same data so the Rails caching will work as expected. And you get the additional benefit of your markup being more semantic (usage of the `<time>` tag), machine-readable (UTC format in the `datetime` attribute), and screen-reader friendly (automatic ARIA labels).

This UI pattern has become a mainstay in nearly every web application and `local_time` is always one of the first gems I add to a Rails project.

**Note:** While this gem is old (released in 2013) and infrequently updated. Don't be scared off thinking that this project is abandoned -- it's simply "done".

## Find Out More

### Hoe References

* home  :: [github.com/basecamp/local_time](https://github.com/basecamp/local_time)
* gem   :: [rubygems.org/gems/local_time](https://rubygems.org/gems/local_time)
* article :: [Basecamp's cache-friendly local time](https://signalvnoise.com/posts/3691-basecamps-cache-friendly-local-time)

