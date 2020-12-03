# Day 3 - blazer Gem - Explore Your Data with SQL. Easily Create Charts and Dashboards. Business Intelligence (BI) Made Simple

Written by {% avatar marckohlbrugge %} [Marc Köhlbrugge](https://github.com/marckohlbrugge)

_Maker of [WIP](https://wip.co) - a community of makers shipping - and of [Startup Jobs](https://startup.jobs), [BetaList](https://betalist.com), [Faces](https://getfaces.com), [among others](https://wip.co/@marc/products)._




## Build better products

We all know building a great product requires understanding the end-user. One way of improving your understanding is to look at their behavior. But who wants to install creepy, third-party Javascript to track our beloved users? Not me.

Fortunately, we already have a lot of data at our disposal. Right in our database!

Enter `blazer`.

Blazer makes it super easy to build a business intelligence dashboard by writing simple SQL queries. It even generates beautiful charts so you can actually understand your data at a glance.

![Signups per Month](i/blazer.png)

## Get notified when something goes wrong

Blazer goes further than just providing a dashboard. It can notify you of things you care about. Just write a query and Blazer will send you an email or Slack message when an SQL query starts returning results you didn't expect.

For example we use the following check to get notified when there are users who signed up last week, but still haven't created any todos:

```SQL
SELECT users.id
FROM users
LEFT OUTER JOIN todos ON todos.user_id = users.id
WHERE  users.created_at BETWEEN (NOW() - '2 week'::interval) AND (NOW() - '1 week'::interval)
GROUP BY users.id
HAVING COUNT(todos.id) = 0
```

## Configuring the gem

Blazer is meant to be added to an existing Ruby on Rails app.

After installing the gem, run the following commands:

```shell
rails generate blazer:install
rails db:migrate
```

Then mount the dashboard in your `config/routes.rb`:

```ruby
mount Blazer::Engine, at: "blazer"
```

Browse to `https://localhost:3000/blazer` to create your first dashboards.

## Find Out More

I strongly advise you to [configure authentication](https://github.com/ankane/blazer#authentication) before deploying to production. The docs include some other great suggestions and advanced examples as well.

### References

- home :: [github.com/ankane/blazer](https://github.com/ankane/blazer)
- gem :: [rubygems.org/gems/blazer](https://rubygems.org/gems/blazer)
- docs :: [rubydoc.info/gems/blazer](https://www.rubydoc.info/gems/blazer/)
