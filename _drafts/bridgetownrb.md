# Day 16 - `bridgetown` gem - Ruby-powered static site generation in 2020

Written by [Pascal Wengerter](https://twitter.com/pascalwengerter)  {% avatar pascalwengerter %}


## The motivation: Keeping Ruby competitive in the JAMstack field

When he figured that Jekyll, arguably the most popular and well-known static site generators in the Ruby community, continues to focus on maintaining extensive backwards-compatibility and will therefore not deliver the modern developer experience around static site generation that would be necessary to keep up with the contenders from other languages (Hugo in Golang, Next.js and Gatsby in JavaScript), [Jared White](https://twitter.com/jaredcwhite) from [Whitefusion](https://whitefusion.io/) set out to provide the world with a competitive open source technology.

Putting his company's experience in deploying numerous Jekyll-based websites and modern best-practices around web development to use, he created a Webpack-aware, Ruby-powered static site generator to allow for top-tier Ruby-based static site generation.


## The status quo: Great content and a bold roadmap

After following the straightforward [setup guide](https://www.bridgetownrb.com/docs/), you can build and preview your site via a live-reload development server. **Markdown** and **Liquid Tags** are supported out of the box, while *themes* and *plugins* can easily be installed. The generated files are being put in an `output` directory.

The roadmap features quite some interesting items and can be found [here](https://www.bridgetownrb.com/about/#roadmap). Contributors are very welcome, the project is well maintained and follows a [code of conduct](https://github.com/bridgetownrb/bridgetown/blob/main/CODE_OF_CONDUCT.md).

If you're still skeptical about the "look and feel", you can watch [Andrew Mason](https://andrewm.codes/) set up a `bridgetown` website using TailwindCSS on [Youtube](https://www.youtube.com/watch?v=UEytpOk9h9w). 

A vivid community on [Discord](https://https://discord.gg/V56yUWR) is happy to answer any questions or can jump in advice if you face any problems.

The deployment is pretty straightforward: Services like Vercel or Netlify provide free hosting tiers and (re-)deployments based on a `git` branch of choice; you can also put the `output` folder behind a standard web server like Apache or NGINX.


## References

* homepage  :: [www.bridgetownrb.com/](https://https://www.bridgetownrb.com/)
* gem   :: [rubygems.org/gems/bridgetown](https://rubygems.org/gems/bridgetown)
* source   :: [github.com/bridgetownrb/bridgetown](https://github.com/bridgetownrb/bridgetown)
* community :: [Discord](https://https://discord.gg/V56yUWR)
