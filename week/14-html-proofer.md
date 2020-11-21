# Week 14 - html-proofer gem - auto-proofread (check and validate) your hypertext (HTML) pages


Let's say you have a project site and want to check that all your hypertext (HTML) pages
open and close all tags according to the specs, that all internal (e.g. `href="#datasets"`)
and external (e.g. `href="http://openbeer.github.io"`) links
are working (no 404's not found errors etc.),
that all images include an alternative text (`alt="Database Schema"`) attribute and so on.


## What’s the html-proofer gem?

Let’s thank Garen Torikian and friends who have bundled up all these checks
and more in a ready-to-use gem, that is, html-proofer.

Let's try it on the Open Mundi (world.db) project site:

~~~
_site
|-- i
|   |-- sqlitestudio.png
|   |-- worlddb-models-place.png
|   `-- worlddb-models.png
|-- build.html
|-- index.html
`-- style.css
~~~

You can run html-proofer using a script in Ruby:

`proof.rb`:

~~~
require 'html/proofer'

HTML::Proofer.new('./site').run
~~~

Or as an alternative use the `htmlproof` command-line tool. Try:

~~~
$ htmlproof --help
~~~

Resulting in:

~~~
htmlproof 2.1.0 -- Runs the HTML-Proofer suite on the files in PATH. For more details, see the README.

Usage:

  htmlproof PATH [options]

Options:
      --as-links     Assumes that `PATH` is a comma-separated array of links to check.
      --alt-ignore image1,[image2,...]  Comma-separated list of Strings or RegExps containing `img`s whose missing `alt` tags are safe to ignore
      --checks-to-ignore check1,[check2,...]   An array of Strings indicating which checks you'd like to not perform.
      --check-external-hash  Checks whether external hashes exist (even if the website exists). This slows the checker down (default: `false`).
      --directory-index-file  Sets the file to look for when a link refers to a directory. (default: `index.html`)
      --disable-external  Disables the external link checker (default: `false`)
      --error-sort SORT  Defines the sort order for error output. Can be `path`, `desc`, or `status` (default: `path`).
      --ext EXT      The extension of your HTML files (default: `.html`)
      --file-ignore file1,[file2,...]  Comma-separated list of Strings or RegExps containing file paths that are safe to ignore
      --href-ignore link1,[link2,...]  Comma-separated list of Strings or RegExps containing `href`s that are safe to ignore.
      --href-swap re:string,[re:string,...]  Comma-separated list of key-value pairs of `RegExp:String`. Transforms links matching `RegExp` into `String`
      --only-4xx     Only reports errors for links that fall within the 4x status code range.
      --check-favicon  Enables the favicon checker (default: `false`).
      --check-html   Enables HTML validation errors from Nokogiri (default: `false`).
      --verbose      Enables more verbose logging.
  -h, --help         Show this message
  -v, --version      Print the name and version
  -t, --trace        Show the full backtrace when an error occurs
~~~

Quite a lineup. Let's try the "standard" checks with verbose logging turned on:

~~~
$ htmlproof --verbose ./_site
~~~

Resulting in:

~~~
Running ["ImageCheck", "ScriptCheck", "LinkCheck"] checks on ./_site on *.html...

Checking imagecheck on ./_site/index.html ...
Checking scriptcheck on ./_site/index.html ...
Checking imagecheck on ./_site/build.html ...
Checking linkcheck on ./_site/index.html ...
Checking scriptcheck on ./_site/build.html ...
Checking linkcheck on ./_site/build.html ...
Checking 25 external links...
Running requests for all 25 external URLs...
Received a 200 for http://openbeer.github.io/ in ./_site/build.html
Received a 200 for http://openfootball.github.io/ in ./_site/build.html
Received a 200 for http://openmundi.github.io/ in ./_site/build.html
Received a 200 for http://opensport.github.io/ in ./_site/build.html
Received a 200 for https://github.com/openmundi/openmundi.github.io in ./_site/build.html
...
Received a 200 for https://github.com/openmundi/world.db/blob/master/north-america/countries.txt in ./_site/index.html
Received a 200 for https://github.com/opensport in ./_site/index.html
Received a 200 for https://github.com/mledoze/countries in ./_site/index.html
Received a 200 for https://github.com/opensport/formula1.db in ./_site/index.html
Received a 200 for https://github.com/opensport/ski.db in ./_site/index.html
Received a 200 for https://github.com/ewheeler/current-countries-of-earth in ./_site/index.html
Received a 200 for https://github.com/geraldb/world.db.admin in ./_site/index.html
Received a 200 for https://github.com/geraldb/world.db.ruby in ./_site/build.html
Received a 404 for https://github.com/openmundi/world.db/blob/master/europe/at-austria/cities.txt in ./_site/index.html
Received a 200 for http://groups.google.com/group/openmundi in ./_site/build.html ./_site/build.html
Received a 200 for https://raw.github.com/openmundi/openmundi.github.io/master/i/sqlitestudio.png in ./_site/build.html
Received a 200 for http://worlddb.herokuapp.com/ in ./_site/index.html
Ran on 2 files!

- ./_site/build.html
  *  image https://raw.github.com/openmundi/openmundi.github.io/master/i/sqlitestudio.png does not have an alt attribute (line 73)
- ./_site/index.html
  *  External link https://github.com/openmundi/world.db/blob/master/europe/at-austria/cities.txt failed: 404 No error
  *  image i/worlddb-models-place.png does not have an alt attribute (line 74)
  *  image i/worlddb-models.png does not have an alt attribute (line 70)

htmlproof 2.1.0 | Error:  HTML-Proofer found 4 failures!
~~~

Bingo! The proof reader found four erros in the two pages (that is, `build.html` and `index.html`).
The link to `at-autstria/cities.txt` is broken (404s)
and all three images miss the required `alt` attribute.

To wrap up lets try two more options, that is, `--check-favicon`
that checks that every page includes a favicon
and `--check-html` that checks that all hypertext markup tags and attributes
are in order using the HTML parser from the Nokogiri gem.

~~~
$ htmlproof --verbose --check-favicon --check-html  ./_site
~~~

Resulting in:

~~~
Checking htmlcheck on ./_site/build.html ...
Checking faviconcheck on ./_site/build.html ...
Checking htmlcheck on ./_site/index.html ...
Checking faviconcheck on ./_site/index.html ...
Ran on 2 files!

- ./_site/build.html
  *  no favicon specified
- ./_site/index.html
  *  no favicon specified

htmlproof 2.1.0 | Error:  HTML-Proofer found 2 failures!
~~~

Oh well that's right. The site has no favicon - but at least
all the hypertext tags are lined-up properly (that is, well-formed
without any validation errors).


## Bonus: Configuring Your Travis Builds w/ Jekyll & HTML Proofreader

Note: If you use a continuous build service (such as Tarvis) you can set it up
to run the HTML proof reading tests on every check-in, for example.

`.travis.yml`:

~~~
language: ruby
rvm:
- 2.1
script: jekyll build && htmlproof ./_site

# branch whitelist
branches:
  only:
  - gh-pages      # test the gh-pages branch

env:
  global:
  - NOKOGIRI_USE_SYSTEM_LIBRARIES=true   # speeds up installation of html-proofer
~~~


## Find Out More

* home     :: [github.com/gjtorikian/html-proofer](https://github.com/gjtorikian/html-proofer)
* gem      :: [rubygems.org/gems/html-proofer](https://rubygems.org/gems/html-proofer)
* rdoc     :: [rubydoc.info/gems/html-proofer](http://rubydoc.info/gems/html-proofer)
* jekyll continuous integration :: [jekyllrb.com/docs/continuous-integration](http://jekyllrb.com/docs/continuous-integration)
