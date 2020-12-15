# Day x - henkei Gem - Extract text content from Word, PowerPoint, and PDF files

Written by {% avatar swanson %} [Matt Swanson](https://github.com/swanson)

_Contrarian-in-training. Building products. Karl Pilkington is my spirit animal. Hacking on [Boring Rails](https://boringrails.com/)._

## Searching within uploaded files

If you've ever built an application that involved file uploads, inevitably you will receive a request to be able to search through those files.

While there are plenty of articles and tools for implementing full-text search with Ruby, nearly all of these examples are for searching your database records. But what if you need to search the contents of a PDF? Or a Microsoft Word document? Or even a PowerPoint presentation? Sounds like a nightmare.

The basic strategy for this problem is to extract as much textual content from the file as your can, break into into chunks -- maybe by page or paragraph -- and then index those chunks in a tool like [ElasticSearch](https://github.com/elastic/elasticsearch-ruby), [Algolia](https://www.algolia.com/doc/api-client/getting-started/install/ruby/?client=ruby), or [PgSearch](https://github.com/Casecommons/pg_search).

But how do you get the text out of these files? It's not as simple as reading a `.txt` file.

## Enter henkei 

The `henkei` gem is a small wrapper around the [Apache Tika project](https://tika.apache.org/).

You can extract the text of any supported file using a common interface:

```ruby
require 'henkei'

data = File.read('TPS Report v2.docx)'
text = Henkei.read(:text, data)
```

Here are some of the formats supported:

* Microsoft Office OLE 2 and Office Open XML Formats (.doc, .docx, .xls, .xlsx, .ppt, .pptx)
* OpenOffice.org OpenDocument Formats (.odt, .ods, .odp)
* Apple iWorks Formats
* Rich Text Format (.rtf)
* Portable Document Format (.pdf)

For the complete list of supported formats, please visit the [Apache Tika Supported Document Formats](http://tika.apache.org/0.9/formats.html) page.

## How it works in practice

In most cloud environments, files are stored on an external service. Henkei can also open a file from a URL:

```ruby
henkei = Henkei.new 'http://my-bucket.s3.aws.com/uploads/2020-projections.pptx'
text = henkei.text
```

Now that you've got any text from the file in a big Ruby `String`, you can use whatever methods you want to split the data into chunks and integrate it into full-text search tools.

```ruby
def extract_text_chunks(s3_url)
  raw_text = Henki.new(s3_url).text
  chunks = []
  chunk = ""
  
  raw_text.split(/[^[[:word:]]]+/).each do |word|
    chunk += "#{word} "
    if chunk.size > MAX_CHUNK_SIZE
      chunks << chunk.squish
      chunk = ""
    end
  end

  chunks.flatten.compact.reject(&:blank?)
end  
```

## Installation Note

One note is that since this gem wraps the Apache Tika library, you will need a Java runtime in your environment to use this gem. It's should not be a problem to add a JRE to most hosting providers, but be aware of this dependendancy.

## Find Out More

### References

* Home  :: [github.com/abrom/henkei](https://github.com/abrom/henkei)
* Gem   :: [rubygems.org/gems/henkei](https://rubygems.org/gems/henkei)
* Docs :: [rubydoc.info/gems/henkei](https://www.rubydoc.info/gems/henkei)
