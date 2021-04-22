# Day 4 - cryptopunks Gem - Christie's Nine Colored in Black 'n' White, Ye Olde' Sepia Tone, Blue or Futuristic False 8-Bit Color Palette - Free Punk Downloads - Right-Click and Save Image


Written by {% avatar geraldb %} [Gerald Bauer](https://github.com/geraldb)


> 9 rare CryptoPunks from the LavraLabs [24×24 pixel] series to
> star in our 21st Century Evening Sale this May [13th] in New York.
>
> All mint numbers under 1k and yes there is a rare Alien!
> Punks [#2](https://www.larvalabs.com/cryptopunks/details/2),
> [#532](https://www.larvalabs.com/cryptopunks/details/532),
> [#58](https://www.larvalabs.com/cryptopunks/details/58),
> [#30](https://www.larvalabs.com/cryptopunks/details/30),
> [#635](https://www.larvalabs.com/cryptopunks/details/635),
> [#602](https://www.larvalabs.com/cryptopunks/details/602),
> [#768](https://www.larvalabs.com/cryptopunks/details/768),
> [#603](https://www.larvalabs.com/cryptopunks/details/603),
> [#757](https://www.larvalabs.com/cryptopunks/details/757).
> [Estimate: $7,000,000-9,000,0000.]
>
> -- [Christie's [- The World's Leading Auction House], April 2021](https://twitter.com/ChristiesInc/status/1380236081472364550)



Let's have a closer looksie at
the Christie's Nine line-up
in the original Larva Labs 24 x 24 pixel design series.
Let's mint a fresh copy.


## Step 0 -  Download the True Official Genuine CryptoPunks™ composite image

One time / first time only - Download the True Official Genuine CryptoPunks™ composite
housing all 10 000 CryptoPunks
in a single 2400×2400 image (~830 kb) for free.
See [`punks.png` »](https://github.com/larvalabs/cryptopunks/blob/master/punks.png)


![](https://github.com/cryptopunksnotdead/cryptopunks/raw/master/cryptopunks/i/punks-zoom.png)



## Step 1 -  Read True Official Genuine CryptoPunks™ composite image


``` ruby
require 'cryptopunks'

punks = Punks::Image::Composite.read( './punks.png' )
```



## Step 2 - Start minting

Note: By default punks get saved in the original 24x24 pixel format
and the first punk starts at index zero, that is, `0`.
running up to 9999.


``` ruby
ids = [2, 532, 58, 30, 635, 602, 768, 603, 757]
ids.each do |id|
  name = '%04d' % id
  punks[id].save( "./punk-#{name}.png" )
end
```

Voila!

![](i/punk-0002.png)
![](i/punk-0532.png)
![](i/punk-0058.png)
![](i/punk-0030.png)
![](i/punk-0635.png)
![](i/punk-0602.png)
![](i/punk-0768.png)
![](i/punk-0603.png)
![](i/punk-0757.png)


Can you spot the super rare alien?


Punk Philosophy Aside: Two New York hipsters auction off million dollar 24 x 24 pixel art. Is that punk?!  Or a cash grab of greedy fraudsters?



Whatever. Fuck the establishment.
Let's generate some freebie punks.


Let's change the color to black & white or
more scientifically known as 8-bit grayscale, that is,
256 shades of gray
starting with black (`0x000000`)
and ending with white (`0xffffff`).


![](i/palette_grayscale.png)


Let's try the magic `grayscale` method:

``` ruby
ids.each do |id|
  name = '%04d' % id

  punk_bw = punks[id].grayscale
  punk_bw.save( "./punk-#{name}_bw.png" )
  punk_bw.zoom( 4 ).save( "./punk-#{name}_bw4x.png" )
end
```


Voila! The Black & White Series.

![](i/punk-0002_bw.png)
![](i/punk-0532_bw.png)
![](i/punk-0058_bw.png)
![](i/punk-0030_bw.png)
![](i/punk-0635_bw.png)
![](i/punk-0602_bw.png)
![](i/punk-0768_bw.png)
![](i/punk-0603_bw.png)
![](i/punk-0757_bw.png)

![](i/punk-0002_bw4x.png)
![](i/punk-0532_bw4x.png)
![](i/punk-0058_bw4x.png)
![](i/punk-0030_bw4x.png)
![](i/punk-0635_bw4x.png)
![](i/punk-0602_bw4x.png)
![](i/punk-0768_bw4x.png)
![](i/punk-0603_bw4x.png)
![](i/punk-0757_bw4x.png)



How does the black & white magic work?
Let's look at the colors
of punk #2 before and after
using the `show_colors` helper
that gathers and prints the insider pixel statistics:


``` ruby
def show_colors( img )
  colors = Hash.new(0)

  img.width.times do |x|
    img.height.times do |y|
      color = img[x,y]
      colors[color] += 1
    end
  end

  puts "#{colors.size} color(s):"
  colors.each_with_index do |(color,count),i|
    print "  [#{i}]  "
    print '%3d pixel(s) - ' % count
    print Color.format( color )
    print "\n"
  end
end
```

Let's try:

```ruby
punk = punks[2]
puts "before:"
show_colors( punk )

puts "after:"
show_colors( punk.grayscale )
```

resulting in:

```
6 color(s):
  [0]  326 pixel(s) - #000000 / rgb(  0   0   0) - hsl(  0°   0%   0%) - α(  0%) - TRANSPARENT
  [1]  165 pixel(s) - #000000 / rgb(  0   0   0) - hsl(  0°   0%   0%)           - BLACK
  [2]   76 pixel(s) - #dbb180 / rgb(219 177 128) - hsl( 32°  56%  68%)
  [3]    4 pixel(s) - #a66e2c / rgb(166 110  44) - hsl( 32°  58%  41%)
  [4]    2 pixel(s) - #d29d60 / rgb(210 157  96) - hsl( 32°  56%  60%)
  [5]    3 pixel(s) - #711010 / rgb(113  16  16) - hsl(  0°  75%  25%)
```

and

```
6 color(s):
  [0]  326 pixel(s) - #000000 / rgb(  0   0   0) - hsl(  0°   0%   0%) - α(  0%) - TRANSPARENT
  [1]  165 pixel(s) - #000000 / rgb(  0   0   0) - hsl(  0°   0%   0%)           - BLACK
  [2]   76 pixel(s) - #b8b8b8 / rgb(184 184 184) - hsl(  0°   0%  72%)           - 8-BIT GRAYSCALE #184
  [3]    4 pixel(s) - #787878 / rgb(120 120 120) - hsl(  0°   0%  47%)           - 8-BIT GRAYSCALE #120
  [4]    2 pixel(s) - #a6a6a6 / rgb(166 166 166) - hsl(  0°   0%  65%)           - 8-BIT GRAYSCALE #166
  [5]    3 pixel(s) - #2d2d2d / rgb( 45  45  45) - hsl(  0°   0%  18%)           - 8-BIT GRAYSCALE #45
```

See the black & white difference?
In the rgb (red/green/blue) color scheme
all colors are now between
`rgb(0 0 0)` and `rgb(255 255 255)` with
every rgb triplet having equal values
and in the hsl (hue/saturation/lightness) color scheme
the hue is now always 0° degree
and the saturation is 0% resulting in
8-bit grayscale, that is,
256 shades of gray
starting with black (`0x000000`)
and ending with white (`0xffffff`).


Now having an 8-bit color palette, that is 256 colors
from 0 to 255 lets us
swap the colors with different 8-bit color palettes.
Let's start with the ye olde' sepia
that is in the real world a chemical process
but in the digital a special effect for
an old fashioned and aged vintage foto look.


![](i/palette_sepia.png)


Let's try:

``` ruby
ids.each do |id|
  name = '%04d' % id

  punk_sepia = punks[id].change_palette8bit( Palette8bit::SEPIA )
  punk_sepia.save( "./punk-#{name}_sepia.png" )
  punk_sepia.zoom( 4 ).save( "./punk-#{name}_sepia4x.png" )
end
```


Voila! The Ye Olde' Sepia Vintage Series.

![](i/punk-0002_sepia.png)
![](i/punk-0532_sepia.png)
![](i/punk-0058_sepia.png)
![](i/punk-0030_sepia.png)
![](i/punk-0635_sepia.png)
![](i/punk-0602_sepia.png)
![](i/punk-0768_sepia.png)
![](i/punk-0603_sepia.png)
![](i/punk-0757_sepia.png)

![](i/punk-0002_sepia4x.png)
![](i/punk-0532_sepia4x.png)
![](i/punk-0058_sepia4x.png)
![](i/punk-0030_sepia4x.png)
![](i/punk-0635_sepia4x.png)
![](i/punk-0602_sepia4x.png)
![](i/punk-0768_sepia4x.png)
![](i/punk-0603_sepia4x.png)
![](i/punk-0757_sepia4x.png)




What's next? Let's bring back colors!

![](i/palette_blue.png)

Let's try a blue theme:

``` ruby
ids.each do |id|
  name = '%04d' % id

  punk_blue = punks[id].change_palette8bit( Palette8bit::BLUE )
  punk_blue.save( "./punk-#{name}_blue.png" )
  punk_blue.zoom( 4 ).save( "./punk-#{name}_blue4x.png" )
end
```


Voila! The Blue Series.

![](i/punk-0002_blue.png)
![](i/punk-0532_blue.png)
![](i/punk-0058_blue.png)
![](i/punk-0030_blue.png)
![](i/punk-0635_blue.png)
![](i/punk-0602_blue.png)
![](i/punk-0768_blue.png)
![](i/punk-0603_blue.png)
![](i/punk-0757_blue.png)

![](i/punk-0002_blue4x.png)
![](i/punk-0532_blue4x.png)
![](i/punk-0058_blue4x.png)
![](i/punk-0030_blue4x.png)
![](i/punk-0635_blue4x.png)
![](i/punk-0602_blue4x.png)
![](i/punk-0768_blue4x.png)
![](i/punk-0603_blue4x.png)
![](i/punk-0757_blue4x.png)



And to wrap up let's go futuristic.

![](i/palette_false.png)


Let's try a false color palette:

``` ruby
ids.each do |id|
  name = '%04d' % id

  punk_false = punks[id].change_palette8bit( Palette8bit::FALSE )
  punk_false.save( "./punk-#{name}_false.png" )
  punk_false.zoom( 4 ).save( "./punk-#{name}_false4x.png" )
end
```

Voila! The Futuristic False Color Series.

![](i/punk-0002_false.png)
![](i/punk-0532_false.png)
![](i/punk-0058_false.png)
![](i/punk-0030_false.png)
![](i/punk-0635_false.png)
![](i/punk-0602_false.png)
![](i/punk-0768_false.png)
![](i/punk-0603_false.png)
![](i/punk-0757_false.png)

![](i/punk-0002_false4x.png)
![](i/punk-0532_false4x.png)
![](i/punk-0058_false4x.png)
![](i/punk-0030_false4x.png)
![](i/punk-0635_false4x.png)
![](i/punk-0602_false4x.png)
![](i/punk-0768_false4x.png)
![](i/punk-0603_false4x.png)
![](i/punk-0757_false4x.png)



Now do-it-yourself (DIY) and generate your own punk series
in your own colors! Yes, you can!



## Find Out More

### References

- Home :: [github.com/cryptopunksnotdead/cryptopunks/cryptopunks](https://github.com/cryptopunksnotdead/cryptopunks/tree/master/cryptopunks)
- Gem :: [cryptopunks](https://rubygems.org/gems/cryptopunks)
- Docs :: [cryptopunks](http://rubydoc.info/gems/cryptopunks)
- Book(let) :: [Programming CryptoPunks & Copypastas Step-by-Step Book / Guide. Inside Unique Pixel Art on the Blockchain...](https://github.com/cryptopunksnotdead/programming-cryptopunks)





