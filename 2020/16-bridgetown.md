# Day 16 - bridgetown Gem - Build Websites (and Apps) the Classic (Static) Compiler Way - Use the Latest JavaScript (Yes, Webpack Out-of-the-Box) or CSS Packages - Fast and Secure By Default


Written by {% avatar pascalwengerter %} [Pascal Wengerter](https://github.com/pascalwengerter)

_A self-taught software developer, and digital all-rounder. Loves democracy 'n' open source software. Black lives matter supporter. Co-founded the open source startup [Matestack](https://matestack.io/). Working as a freelancer while [looking for a role in (digital) product management](https://pascal.rocks/about)._



## The motivation: Keeping Jekyll up-to-date in the modern web (and apps) world (built with the latest JavScript and CSS packages)

When {% avatar jaredcwhite size=20 %}
[Jared White](https://github.com/jaredcwhite) (from [Whitefusion](https://whitefusion.io/))
 figured that Jekyll, arguably the most popular and well-known (static) website generator in the Ruby community, continues to focus on maintaining extensive backwards-compatibility and will therefore not deliver the modern developer experience around static website generation that would be necessary to keep up with the contenders from other languages (Hugo in Go, Next.js and Gatsby.js in JavaScript),
 Jared set out to work on a fork of Jekyll with a brand new set of project goals and a future roadmap.
 
Putting his company's experience in building numerous Jekyll-based websites 
and modern best-practices around web development to use, he created Bridgetown - a new Webpack-aware, Ruby-powered static website generator.


## The status quo: Great content and a bold roadmap

After following the [setup guide](https://www.bridgetownrb.com/docs/), you can build and preview your website via a live-reload development server. Markdown (to HTML conversion) and Liquid tags in page templates are supported out of the box, 
while themes and plugins can easily be installed. The generated files are being put in an `output` directory.

The roadmap features quite some interesting items. Contributors are welcome, the project is well maintained and follows a code of conduct.

If you're still skeptical about the "look and feel", you can watch
{% avatar andrewmcodes size=20 %} [Andrew Mason](https://github.com/andrewmcodes) 
on "How to Set up a `bridgetown` website using Tailwind CSS".

A vivid community on Discord is happy to answer any questions or can jump in advice if you face any problems.

For going live publishing - website hosting services like Vercel 
or Netlify offer free tiers and (re-)deployments based on a `git` branch of choice; 
you can also put the `output` folder behind a standard web server like Apache or Nginx.



## References

* Home  :: [bridgetownrb.com](https://www.bridgetownrb.com/)
* Gem   :: [rubygems.org/gems/bridgetown](https://rubygems.org/gems/bridgetown)
* Source   :: [github.com/bridgetownrb/bridgetown](https://github.com/bridgetownrb/bridgetown)
* Roadmap   :: [The History and Future of Bridgetown](https://www.bridgetownrb.com/about/#roadmap)
* Community :: [Discord](https://discord.gg/V56yUWR)
* Video     :: [Set up a `bridgetown` website using Tailwind CSS](https://www.youtube.com/watch?v=UEytpOk9h9w)

