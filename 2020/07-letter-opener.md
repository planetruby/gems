# Day 7 - letter_opener Gem - Let Your Browser (Automagically) Open-Up Your Email Letters for Previewing. Stop Sending Emails to Yourself for Testing


Written by {% avatar swanson %} [Matt Swanson](https://github.com/swanson)

_Contrarian-in-training. Building products. Karl Pilkington is my spirit animal. Hacking on [Boring Rails](https://boringrails.com/) and [Slotback Labs](https://slotbacklabs.com/)._



## Have you ever sent yourself a test email?

You're working on adding an email notification in your app and you need to try it out. Time to send yourself a test email...or two...or fifty... We've all done it.

But when you're writing code for your Mailers, wouldn't it be great if you could quickly preview the email message and iterate on the content?

Enter `letter_opener`.

## Change how emails are 'delivered' in your development environment

Rails supports changing your application configuration based on environment. So, for example, you can change how emails are sent through `ActionMailer` on a per-environment basis.

The `letter_opener` gem takes advantage of this by allowing you to switch out how emails are sent. Instead of actually delivering an email notification from your code, the gem will instead open the email in your default browser so that you can preview the contents and inspect the contents (as well as the `To`, `From`, `Subject`, and other options).

While Rails does provide a [mechanism to preview emails](https://guides.rubyonrails.org/action_mailer_basics.html#previewing-emails), `letter_opener` is much more convenient because you don't have to write special previews for every email you want to test or manually visit the preview URLs. _Any_ emails you send via `ActionMailer` will be intercepted and shown in the browser.

## Configuring the gem

After installing the gem, simply make the following configuration changes in your `config/environments/development.rb`:

```ruby
config.action_mailer.delivery_method = :letter_opener
config.action_mailer.perform_deliveries = true
```

Restart your application server if it was running and voila! Now you can preview emails in the browser and never resort to sending test emails to your personal account :).

## Find Out More

### References

- Home :: [github.com/ryanb/letter_opener](https://github.com/ryanb/letter_opener)
- Gem :: [rubygems.org/gems/letter_opener](https://rubygems.org/gems/letter_opener)
- Docs :: [rubydoc.info/gems/letter_opener](https://www.rubydoc.info/gems/letter_opener/)
