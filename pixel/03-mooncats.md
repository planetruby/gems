# Day 3 - mooncats Gem - Generate Your Own All-25 440-MoonCats In-One Image - mooncatrescue.png Composite (2400×6120)


Written by {% avatar geraldb %} [Gerald Bauer](https://github.com/geraldb)



> Mooncats are scarce and verifiable.
> You can bring a mooncat anywhere in the world, airplane or boat,
> proving itself to be a truer store of value than gold itself.
>
> Only 25 thousand mooncats. 21 million bitcoins. Do the math.
>
> Someone took Jeff Bezos crown today [as the richest man on earth].
> Top mooncat holder owns 1,144 cats. LOL.
>
> I can right click and save a mooncat [pixel image for free].
>
> -- MoonCatRescue Mania, March 2021



## The World's First Billion Dollar Pixel Image

Inspired by the composite image (2400×2400) that holds all 10 000 cryptopunks
in the original 24×24 design:

![](https://github.com/cryptopunksnotdead/awesome-cryptopunks-bubble/raw/master/i/punks-zoom.png)



Let's put together an all-in-one composite version for MoonCatRescue.
Yes, you can.



## Step 1 - Read the metadata (5-byte hexstring ids) of all 25 440 MoonCats


Let's use the sister [`/mooncatrescue`](https://github.com/cryptocopycats/mooncatrescue) project - a free public domain
dataset of all 25 440 MoonCats
in comma-separated values (CSV) format
in blocks of a thousand cats each
(e.g.
`00.csv` incl. 0x0000020886-0x000a05b9ac,
`01.csv` incl. 0x000a0a11c8-0x0013b68a2f,
`02.csv` incl. 0x0013b72770-0x001e26e929,
and so on).
The data records for cats
incl. id, palette, pattern, pose, facing, face, fur, colors in rbg and hsl (hue, sat, lum), row and mint serial number,
and more.
Example - [`00.csv`](https://github.com/cryptocopycats/mooncatrescue/blob/master/00.csv):


```
row, id,           palette, pattern, pose, facing, face, fur, color,    ... mint
0,   0x0000020886, Normal, 0, Standing, Left, Smile, Solid, Sky Blue,   ... 2679
1,   0x000002f63e, Normal, 0, Standing, Left, Smile, Solid, Green,      ... 13869
2,   0x000004683b, Normal, 0, Standing, Left, Smile, Solid, Lime Green, ... 24457
3,   0x0000048998, Normal, 0, Standing, Left, Smile, Solid, Cyan,       ... 22386
4,   0x000006ce5d, Normal, 0, Standing, Left, Smile, Solid, Green,      ... 7933
...
```


Download a copy of the dataset - use the zip archive (or git clone)
and let's read in all records:


``` ruby
require 'mooncats'

data = Mooncats::Dataset.read( './mooncatrescue/*.csv' )

puts "  #{data.size} mooncat(s)"
```

printing:

```
  25440 mooncat(s)
```



## Step 2 - Generate the Composite of all 25 440 MoonCats Together in One (Big) Image

Let's use the composite image (convenience) helper / wrapper
that defaults to `columns=100, rows=255`, that is,
100 mooncats per row and 255 rows total (100×255 = 25 500 mooncats max.)
and using a 24×24 canvas for every mooncat, thus,
resulting in a (big) 2400×6120 pixel image.


``` ruby
composite = Mooncats::Image::Composite.new


data.each_with_index do |cat_meta,i|
  cat_id = cat_meta['id']

  puts "  adding [#{i}] - #{cat_id}..."

  composite << Mooncats::Image.generate( cat_id )
end
```

resulting in:

```
  adding [0] - 0000020886...
  adding [1] - 000002f63e...
  adding [2] - 000004683b...
  adding [3] - 0000048998...
  adding [4] - 000006ce5d...
  adding [5] - 0000072d16...
  adding [6] - 000007a6a5...
  adding [7] - 00000800fa...
  adding [8] - 00000cfd6f...
  adding [9] - 00000d9506...
  adding [10] - 000017a67a...
  adding [11] - 00001c0d3d...
  ...
```


Let's save the "monster" - note: depending on your computer machine power it might take some seconds.

``` ruby
composite.save( 'mooncatrescue.png', :best_compression )
```


And voila!

![](https://github.com/cryptocopycats/awesome-mooncatrescue-bubble/raw/master/i/mooncatrescue-zoom.png)



Note: The mooncat listing order follows the mooncatrescue.csv dataset, that is, ordered by (5-byte hex string) ids e.g. `0x0000020886`, `0x000002f63e`, `0x000004683b`, and so on.






## Frequently Asked Questions (F.A.Q.s) and Answers

Q: Can I download a copy?

A: Yes, see [`mooncatrescue.png`](https://github.com/cryptocopycats/awesome-mooncatrescue-bubble/blob/master/i/mooncatrescue.png) to download all 25 440 MoonCats in a 2400×6120 image (~ 6 MB) for free.


Q: Can I (re)sort by pose, facing, face, and fur in that order, for example?

A: Yes, you can (re)sort the data before generating the composite
as you like.
Let's (re)sort the data
by pose, facing, face, fur in that order, for example:

``` ruby
data = data.sort do |l,r|   # l=left, r=right
          res = l.pose   <=> r.pose                 # 1. sort by pose
          res = l.facing <=> r.facing  if res == 0  # 2. sort by facing if pose is equal (0)
          res = l.face   <=> r.face    if res == 0  # 3. sort by face if facing is equal (0) too
          res = l.fur    <=> r.fur     if res == 0  # 4. sort by fur if face is equal (0) too
          res     # returns -1,0,1  (-1 if smaller, 0 if equal, 1 if greater)
       end
```



## Find Out More

### References

- Home :: [github.com/cryptocopycats/mooncats/mooncats](https://github.com/cryptocopycats/mooncats/tree/master/mooncats)
- Gem :: [mooncats](https://rubygems.org/gems/mooncats)
- Docs :: [mooncats](http://rubydoc.info/gems/mooncats)

