# Day 14 - que-scheduler gem - bulletproof, infrastructure-free asynchronous job scheduling

Written by [Harry Lascelles](https://twitter.com/hlascelles)

A performant web application should always make sure it does the heavy lifting outside of user
requests where possible. Many asynchronous job systems have sprung up to solve this problem,
such as Resque, Sidekiq, DelayedJob and Que.

However sometimes these workloads are not even driven - they are time driven. For that you need a
reliable scheduler - you need [que-scheduler](https://github.com/hlascelles/que-scheduler).

## The gem

The [que-scheduler](https://github.com/hlascelles/que-scheduler) gem is a simple and dependable way to get up and running with job scheduler that
doesn't require any extra infrastructure. If you are already running the [que](https://github.com/que-rb/que) job
system, then you are good to go. No Redis is required, no external processes need to be installed or
synchronised and nothing needs to run in HA.

Install and apply the setup migration to get started:

```ruby
gem 'que-scheduler'

# In a migration...
Que::Scheduler::Migrations.migrate!(version: 5)
```

## Built on the que bedrock

The [que-scheduler](https://github.com/hlascelles/que-scheduler) gem is for use 
with the high performance [que](https://github.com/que-rb/que) job system. 
Unlike systems that use Redis as a backend, que keeps all of the job logic in the (postgres) database.
This has all the advantages you'd expect from an ACID DB. If you schedule job, then
either the job row will get committed when the transaction completes, or it will be cleaned up 
if the transaction errors. To avoid worker conflict que uses advisory locks on rows to make sure
jobs are run by one worker, once.

[que-scheduler](https://github.com/hlascelles/que-scheduler) takes advantage of these facts 
by running itself as a que job. As long as you have workers
running, then your scheduler is running. If you deploy to a new environment, then running your 
standard migrations automatically starts and configures the scheduler. If you back up the DB,
then the schedule is backed up. There are other features too, such as job coalescing and audit tables. 

## Configuration

The config is held in a yaml file, and is self explanatory:

```yaml
# Simple example
CancelAbandonedOrders:
  cron: "*/5 * * * *"

# Altogether now
all_options_job:
  cron: "0 7 * * * America/Los_Angeles"
  class: QueueDocuments
  queue: reporting
  priority: 25
  args: ['open']
```

If you are using a Redis backed job system and don't feel it is working out for you, or want to 
simplify your infrastructure whilst also improving job enqueueing semantics, que and que-scheduler 
could be the solution you are looking for.

If you're already using que, give [que-scheduler](https://github.com/hlascelles/que-scheduler) a go today.

## Find Out More

### References

* home  :: [github.com/hlascelles/que-scheduler](https://github.com/hlascelles/que-scheduler)
* gem   :: [rubygems.org/gems/que-scheduler](https://rubygems.org/gems/que-scheduler)
* que   :: [que-rb/que](https://github.com/que-rb/que)
