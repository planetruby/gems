# push-notifications gem - iOS, Android and Windows Phone Push Notifications made easy


Do you have a mobile app? Do you push notifications to them? Do you want to keep it easy? Read on!

[Push Notifications](https://github.com/calonso/ruby-push-notifications) makes it easy to Push Notifications to you users' devices by providing a common interface, regardless of the device's manufacturer (currently iOS, Android and Windows Phone supported).

The general steps to push a notification are (Using iOS interface for this example):

1. Create one (or more) notifications.

``` ruby
tokens = [
  'First token here',
  'Second token here'
]

notification = RubyPushNotifications::APNS::APNSNotification.new tokens, { aps: { alert: 'Hello APNS World!', sound: 'true', badge: 1 } }
```

2. Create the corresponding pusher

``` ruby
pusher = RubyPushNotifications::APNS::APNSPusher.new(File.read('/path/to/your/apps/certificate.pem'), true)
```

3. Push!

``` ruby
pusher.push [notification]
```

4. Get Feedback

``` ruby
p 'Notification sending results:'
p "Success: #{notification.success}, Failed: #{notification.failed}"
```

And that's it!


For more detailed examples:

1. [Apple iOS example](https://github.com/calonso/ruby-push-notifications/blob/master/examples/apns.rb)
2. [Google GCM example](https://github.com/calonso/ruby-push-notifications/blob/master/examples/gcm.rb)
3. [Windows Phone example](https://github.com/calonso/ruby-push-notifications/blob/master/examples/mpns.rb) (thanks [@lazaronixon](https://github.com/lazaronixon))


## Find more

* Home     : [github.com/calonso/ruby-push-notifications](https://github.com/calonso/ruby-push-notifications)
* RubyGems : [rubygems.org/gems/ruby-push-notifications](https://rubygems.org/gems/ruby-push-notifications)
* Docs     : [rubydoc.info/gems/ruby-push-notifications](http://rubydoc.info/gems/ruby-push-notifications)
