---
layout: gem
title:  "Week #4 - kramdown gem - turn easy-to-read and easy-to-write wiki-style plain text in markdown into hypertext"
---

## What's Markdown?

Markdown is an easy-to-write and easy-to-read wiki-style markup language that
lets you author web pages in plain text. Example:

~~~
## What's `football.db`?

A free and open public domain football database & schema
for use in any (programming) language (e.g. uses plain datasets). Example:

    ### Teams
    
    barcelona, Barcelona|FC Barcelona|Fútbol Club Barcelona, BAR
    madrid,    Real Madrid|Real Madrid CF,                   RMD
    ...
~~~

becomes

~~~
<h2>What's <code>football.db</code>?</h2>

<p>A free and open public domain football database &amp; schema
for use in any (programming) language (e.g. uses plain datasets). Example:</p>

<pre><code>
### Teams

barcelona, Barcelona|FC Barcelona|Fútbol Club Barcelona, BAR
madrid,    Real Madrid|Real Madrid CF,                   RMD
...
</code></pre>
~~~


## What's `kramdown`?

[`kramdown`](https://github.com/gettalong/kramdown) is a gem that
that lets you convert Markdown (`.md, .mkdwn, .markdown`) to Hypertext (`.html`).
Thanks to [Thomas Leitner](https://github.com/gettalong) from Vienna, Austria
for polishing the gem - more than 30+ releases - leading to today's version 1.5.0.

## Usage

Converting your wiki-style plain text markup
to hypertext using kramdown is as easy as:

~~~
require 'kramdown'

Kramdown::Document.new( '¡Barça, Barça, Baaarça!' ).to_html

# => "<p>¡Barça, Barça, Baaarça!</p>\n"
~~~

## Let's create another static site generator in 5 minutes

First lets create a new ruby script and
let's add the `kramdown` gem
plus the `find` standard Ruby library module to help us with finding files. Example:


`./sitegen.rb`:

~~~
require 'kramdown'
require 'find'
~~~

Next let's make a `_site` output folder.

~~~
SITE_PATH = './_site'

Dir.mkdir( SITE_PATH ) unless File.exist?( SITE_PATH )
~~~

Now let's add a `markdown` converter helper method.

~~~
def markdown( text )
  Kramdown::Document.new( text ).to_html
end
~~~

Finally, to wrap up let's loop over all files in the current working folder, that is, `.`
and generate hypertext (`.html`) documents in the `./site` folder
from the markdown (`.md`) source documents.

~~~
Find.find('.') do |path|
  if File.extname(path) == '.md'              # e.g. ./index.md => .md
    basename = File.basename(path, '.md')     # e.g. ./index.md => index

    File.open( "#{SITE_PATH}/#{basename}.html", 'w') do |file|
      file.write markdown( File.read( path ) )
    end
  end
end
~~~

That's it. Create a new markdown file. Example:

`./index.md`:

~~~
## What's `football.db`?

A free and open public domain football database & schema
for use in any (programming) language (e.g. uses plain datasets). Example:

    ### Teams
    
    barcelona, Barcelona|FC Barcelona|Fútbol Club Barcelona, BAR
    madrid,    Real Madrid|Real Madrid CF,                   RMD
    ...
~~~

Run the static site generation machinery e.g. type:

~~~
$ ruby ./sitegen.rb
~~~

That's it. Open up the web page `./_site/index.html` in your browser. 



## Bonus: `kramdown` Command Line Tool

Note: The `kramdown` gem ships with a command line tool named - suprise,
suprise - `kramdown`. Type in your shell:

~~~
$ kramdown -h
~~~

resulting in:

~~~
Command line options:

    -i, --input ARG
          Specify the input format: kramdown (default), html, GFM or markdown
    -o, --output ARG
          Specify one or more output formats separated by commas: html (default), kramdown, latex, pdf or remove_html_tags
    -v, --version
          Show the version of kramdown
    -h, --help
          Show the help

kramdown options:

        --template ARG
          The name of an ERB template file that should be used to wrap the output
          or the ERB template itself.
          
          This is used to wrap the output in an environment so that the output can
          be used as a stand-alone document. For example, an HTML template would
          provide the needed header and body tags so that the whole output is a
          valid HTML file. If no template is specified, the output will be just
          the converted text.
          
          When resolving the template file, the given template name is used first.
          If such a file is not found, the converter extension (the same as the
          converter name) is appended. If the file still cannot be found, the
          templates name is interpreted as a template name that is provided by
          kramdown (without the converter extension). If the file is still not
          found, the template name is checked if it starts with 'string://' and if
          it does, this prefix is removed and the rest is used as template
          content.
          
          kramdown provides a default template named 'document' for each converter.
          
          Default: ''
          Used by: all converters

        --[no-]auto-ids
          Use automatic header ID generation
          
          If this option is `true`, ID values for all headers are automatically
          generated if no ID is explicitly specified.
          
          Default: true
          Used by: HTML/Latex converter

        --[no-]auto-id-stripping
          Strip all formatting from header text for automatic ID generation
          
          If this option is `true`, only the text elements of a header are used
          for generating the ID later (in contrast to just using the raw header
          text line).
          
          This option will be removed in version 2.0 because this will be the
          default then.
          
          Default: false
          Used by: kramdown parser

        --auto-id-prefix ARG
          Prefix used for automatically generated header IDs
          
          This option can be used to set a prefix for the automatically generated
          header IDs so that there is no conflict when rendering multiple kramdown
          documents into one output file separately. The prefix should only
          contain characters that are valid in an ID!
          
          Default: ''
          Used by: HTML/Latex converter

        --[no-]transliterated-header-ids
          Transliterate the header text before generating the ID
          
          Only ASCII characters are used in headers IDs. This is not good for
          languages with many non-ASCII characters. By enabling this option
          the header text is transliterated to ASCII as good as possible so that
          the resulting header ID is more useful.
          
          The stringex library needs to be installed for this feature to work!
          
          Default: false
          Used by: HTML/Latex converter

        --[no-]parse-block-html
          Process kramdown syntax in block HTML tags
          
          If this option is `true`, the kramdown parser processes the content of
          block HTML tags as text containing block-level elements. Since this is
          not wanted normally, the default is `false`. It is normally better to
          selectively enable kramdown processing via the markdown attribute.
          
          Default: false
          Used by: kramdown parser

        --[no-]parse-span-html
          Process kramdown syntax in span HTML tags
          
          If this option is `true`, the kramdown parser processes the content of
          span HTML tags as text containing span-level elements.
          
          Default: true
          Used by: kramdown parser

        --[no-]html-to-native
          Convert HTML elements to native elements
          
          If this option is `true`, the parser converts HTML elements to native
          elements. For example, when parsing `<em>hallo</em>` the emphasis tag
          would normally be converted to an `:html` element with tag type `:em`.
          If `html_to_native` is `true`, then the emphasis would be converted to a
          native `:em` element.
          
          This is useful for converters that cannot deal with HTML elements.
          
          Default: false
          Used by: kramdown parser

        --link-defs ARG
          Pre-defines link definitions
          
          This option can be used to pre-define link definitions. The value needs
          to be a Hash where the keys are the link identifiers and the values are
          two element Arrays with the link URL and the link title.
          
          If the value is a String, it has to contain a valid YAML hash and the
          hash has to follow the above guidelines.
          
          Default: {}
          Used by: kramdown parser

        --footnote-nr ARG
          The number of the first footnote
          
          This option can be used to specify the number that is used for the first
          footnote.
          
          Default: 1
          Used by: HTML converter

        --entity-output ARG
          Defines how entities are output
          
          The possible values are :as_input (entities are output in the same
          form as found in the input), :numeric (entities are output in numeric
          form), :symbolic (entities are output in symbolic form if possible) or
          :as_char (entities are output as characters if possible, only available
          on Ruby 1.9).
          
          Default: :as_char
          Used by: HTML converter, kramdown converter

        --toc-levels ARG
          Defines the levels that are used for the table of contents
          
          The individual levels can be specified by separating them with commas
          (e.g. 1,2,3) or by using the range syntax (e.g. 1..3). Only the
          specified levels are used for the table of contents.
          
          Default: 1..6
          Used by: HTML/Latex converter

        --line-width ARG
          Defines the line width to be used when outputting a document
          
          Default: 72
          Used by: kramdown converter

        --latex-headers ARG
          Defines the LaTeX commands for different header levels
          
          The commands for the header levels one to six can be specified by
          separating them with commas.
          
          Default: section,subsection,subsubsection,paragraph,subparagraph,subparagraph
          Used by: Latex converter

        --smart-quotes ARG
          Defines the HTML entity names or code points for smart quote output
          
          The entities identified by entity name or code point that should be
          used for, in order, a left single quote, a right single quote, a left
          double and a right double quote are specified by separating them with
          commas.
          
          Default: lsquo,rsquo,ldquo,rdquo
          Used by: HTML/Latex converter

        --[no-]remove-block-html-tags
          Remove block HTML tags
          
          If this option is `true`, the RemoveHtmlTags converter removes
          block HTML tags.
          
          Default: true
          Used by: RemoveHtmlTags converter

        --[no-]remove-span-html-tags
          Remove span HTML tags
          
          If this option is `true`, the RemoveHtmlTags converter removes
          span HTML tags.
          
          Default: false
          Used by: RemoveHtmlTags converter

        --header-offset ARG
          Sets the output offset for headers
          
          If this option is c (may also be negative) then a header with level n
          will be output as a header with level c+n. If c+n is lower than 1,
          level 1 will be used. If c+n is greater than 6, level 6 will be used.
          
          Default: 0
          Used by: HTML converter, Kramdown converter, Latex converter

        --[no-]hard-wrap
          Interprets line breaks literally
          
          Insert HTML `<br />` tags inside paragraphs where the original Markdown
          document had newlines (by default, Markdown ignores these newlines).
          
          Default: true
          Used by: GFM parser

        --syntax-highlighter ARG
          Set the syntax highlighter
          
          Specifies the syntax highlighter that should be used for highlighting
          code blocks and spans. If this option is set to +nil+, no syntax
          highlighting is done.
          
          Options for the syntax highlighter can be set with the
          syntax_highlighter_opts configuration option.
          
          Default: coderay
          Used by: HTML converter

        --syntax-highlighter-opts ARG
          Set the syntax highlighter options
          
          Specifies options for the syntax highlighter set via the
          syntax_highlighter configuration option.
          
          The value needs to be a hash with key-value pairs that are understood by
          the used syntax highlighter.
          
          Default: {}
          Used by: HTML converter

        --math-engine ARG
          Set the math engine
          
          Specifies the math engine that should be used for converting math
          blocks/spans. If this option is set to +nil+, no math engine is used and
          the math blocks/spans are output as is.
          
          Options for the selected math engine can be set with the
          math_engine_opts configuration option.
          
          Default: mathjax
          Used by: HTML converter

        --math-engine-opts ARG
          Set the math engine options
          
          Specifies options for the math engine set via the math_engine
          configuration option.
          
          The value needs to be a hash with key-value pairs that are understood by
          the used math engine.
          
          Default: {}
          Used by: HTML converter
~~~

Yes, it's well documented. Welcome to the wonders of `kramdown`.


## Find Out More 

* home  :: [github.com/gettalong/kramdown](https://github.com/gettalong/kramdown)
* gem   :: [rubygems.org/gems/kramdown](https://rubygems.org/gems/kramdown)
* rdoc  :: [rubydoc.info/gems/kramdown](http://rubydoc.info/gems/kramdown)

