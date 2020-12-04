# Day 12 - `matestack-ui-core` gem - alternative view layer for RubyOnRails

Written by [Pascal Wengerter](https://twitter.com/pascalwengerter)  {% avatar pascalwengerter %}


## The idea: Writing web views in pure Ruby

Deeply in love with programming in Ruby and frustrated with the overly complex state of fullstack web development, [Jonas Jabari](https://twitter.com/jonasjabari) started experimenting in late 2017 and ended up creating a prototype that eventually grew into `matestack-ui-core` and subsequent libraries.

Instead of rendering `.erb`-views or introducing a separate SPA-framework to handle the frontend, `matestack-ui-core` introduces
- a new way of layouting your frontend based on the concepts of `apps`, `pages` and `components`
- a core library of components (Ruby methods) representing the canonical HTML tags
- some advanced core components that abstract away common JavaScript functionality to save you from re-implementing the same dynamic behavior
- the possibility to easily create custom components, both of static and dynamic nature
- multiple approaches to incrementally introduce it to existing projects

The gem is built using `trailblazer-cells` and `Vue.js`.

### Use case 1: Static views in Ruby

Below is a simple example containing both a simple component and page. The `div` and `paragraph` core components get orchestrated by the (custom) `Some::Component`, which then gets called on the `ExamplePage`:

```ruby
  # defining a component
  class Some::Component < Matestack::Ui::Component

    def prepare
      @some_data = "some data"
    end

    def response
      div id: "my-component" do
        paragraph text: @some_data
      end
    end

  end

# using the component on a page (= web view)
class ExamplePage < Matestack::Ui::Page

  def response
    div id: "div-on-page" do
      some_component
    end
  end

end
```

turns into

```html
<div id="div-on-page">
  <div id="my-component">
    <p>some data</p>
  </div>
</div>
```

### Use case 2: Dealing with repetitive JavaScript behavior

There's a handful of JavaScript functionality that
1. has a good value proposition from the business perspective and needs to be implemented properly 
2. is not '*new*' and won't teach you (the developer) anything, but you end up having to do it anyhow

Leveraging the power of `Vue.js`, the `matestack-ui-core` gem has abstracted away quite a number of use cases and turned them into (arguably) straighforward solutions. Judge for yourself:

```ruby
# app/matestack/components/some_component.rb

class Components::SomeComponent < Matestack::Ui::Component

  def response
    onclick emit: "some_event" do
      button text: "click me"
    end
    toggle show_on: "some_event", hide_after: 5000 do
      plain "Oh yes! You clicked me!"
    end
  end

end
```

turns into

```html
<button tabindex="0">click me</button>
<!-- only gets shown when the buttons is clicked & gets hidden after 5000ms
<div class="matestack-toggle-component-root"><br>Oh yes! You clicked me!</div>
-->
```

### Use case 2: Dealing with repetitive JavaScript behavior

There's a handful of JavaScript functionality that
1. has a good value proposition from the business perspective and needs to be implemented properly 
2. is not '*new*' and won't teach you (the developer) anything, but you end up having to do it anyhow

Leveraging the power of `Vue.js`, the `matestack-ui-core` gem has abstracted away quite a number of use cases and turned them into (arguably) straighforward solutions. Judge for yourself:

```ruby
# app/matestack/components/some_component.rb

class Components::SomeComponent < Matestack::Ui::Component

  def response
    onclick emit: "some_event" do
      button text: "click me"
    end
    toggle show_on: "some_event", hide_after: 5000 do
      plain "Oh yes! You clicked me!"
    end
  end

end
```

turns into

```html
<button tabindex="0">click me</button>
<!-- only gets shown when the buttons is clicked & gets hidden after 5000ms
<div class="matestack-toggle-component-root"><br>Oh yes! You clicked me!</div>
-->
```

### Use case X: Creating reactive custom components, re-using legacy views, advanced JavaScript behavior ...

Please refer to the guides, this should only be a teaser ;)

## Find Out More

### References

* homepage  :: [matestack.io](https://matestack.io)
* gem   :: [rubygems.org/gems/matestack-ui-core/](https://rubygems.org/gems/matestack-ui-core/)
* source :: [github.com/matestack/matestack-ui-core](https://github.com/matestack/matestack-ui-core)

### Installation & Guides

The [installation](https://docs.matestack.io/docs/start/100-installation/) is quite straightforward and works with both the Asset Pipeline and Webpacker. Along with more information, there's also a [10 step tutorial on how to set up & deploy your first matestack-powered Rails app to Heroku](https://docs.matestack.io/docs/reactive_apps/1000-tutorial) and the full [Component API](https://docs.matestack.io/docs/api/README.md).

### Community

There's a [Discord](https://discord.gg/c6tQxFG) server, Q&A calls twice a week and contribution opportunities for anyone that wants to get involved!

### Feedback

Feel free to reach out to [Jonas Jabari](mailto:jonas@matestack.io) if you got anything to share or ask :)
