# Day 5 - pixelart-characters Gem - Generate Pixel Art Characters, Algorithmically - Case Study №1 - 8-Bit Icon Avatar Series (20x20)


Written by {% avatar geraldb %} [Gerald Bauer](https://github.com/geraldb)



## Case Study №1 - 8-Bit Icon Series (20x20)

Let's try the 8-bit icon series with a 20x20 pixel art image set,
see [/design.punks/8bit](https://github.com/cryptopunksnotdead/design.punks#8-bit-icon-series-20x20).


Step 1: Map the pixel art images
(or designs in ascii text)
to a characters generation definition.


``` ruby
require 'pixelart/characters'

######
# skin tone colors
HUMAN_LIGHTER_BASE1 = '0xead9d9'   # rgb(234 217 217) - hsl(  0°  29%  88%)
HUMAN_LIGHT_BASE1   = '0xdbb180'   # rgb(219 177 128) - hsl( 32°  56%  68%)
HUMAN_DARK_BASE1    = '0xae8b61'   # rgb(174 139  97) - hsl( 33°  32%  53%)
HUMAN_DARKER_BASE1  = '0x713f1d'   # rgb(113  63  29) - hsl( 24°  59%  28%)


###############
# characters generation definition
CHARACTERS = {
  'male'   => {
    'face' => [{ design: 'male', colors: [HUMAN_LIGHTER_BASE1] },
               { design: 'male', colors: [HUMAN_LIGHT_BASE1] },
               { design: 'male', colors: [HUMAN_DARK_BASE1] },
               { design: 'male', colors: [HUMAN_DARKER_BASE1] }],
    'clothes' => (1..65).to_a,
    'eye'     => (1..32).to_a,
    'mouth'   => (1..26).to_a,
    'hair'    => (1..36).to_a,
  },
  'female' => {
     'face' => [{ design: 'female', colors: [HUMAN_LIGHTER_BASE1] },
                { design: 'female', colors: [HUMAN_LIGHT_BASE1] },
                { design: 'female', colors: [HUMAN_DARK_BASE1] },
                { design: 'female', colors: [HUMAN_DARKER_BASE1] }],
     'clothes' => (1..59).to_a,
     'eye'     => (1..53).to_a,
     'mouth'   => (1..17).to_a,
     'hair'    => (1..33).to_a,
  }
}
```


Note: List all parts (such as face, clothes, eyes, etc.) that make up
a character in the paste order, that is,
the algo will generate an empty pixel image / canvas and than adds - or is that composes or pastes - one part after the other on top all the way from face to hair.



Let's set up the character generator (or is that artist?) by
passing in the characters definition and
the root directory housing all the the pixel art:

``` ruby
artist = Character.new( CHARACTERS,
                        dir: "./8bit" )
```


Let's generate some characters.
Let's start with a male using the part selection (1,1,1,1,1) - that is,
face #1, clothes #1, eye #1, mouth #1, and hair #1 -
and so on:


``` ruby
img = artist.generate( 'male', 1, 1, 1, 1, 1 )
img.save( '8bit-male1.png')
img.zoom(4).save( '8bit-male1x4.png')

img = artist.generate( 'male', 2, 2, 2, 2, 2 )
img.save( '8bit-male2.png')
img.zoom(4).save( '8bit-male2x4.png')

img = artist.generate( 'male', 3, 3, 3, 3, 3 )
img.save( '8bit-male3.png')
img.zoom(4).save( '8bit-male3x4.png')

img = artist.generate( 'male', 4, 4, 4, 4, 4 )
img.save( '8bit-male4.png')
img.zoom(4).save( '8bit-male4x4.png')


img = artist.generate( 'female', 1, 1, 1, 1, 1 )
img.save( '8bit-female1.png')
img.zoom(4).save( '8bit-female1x4.png')

img = artist.generate( 'female', 2, 2, 2, 2, 2 )
img.save( '8bit-female2.png')
img.zoom(4).save( '8bit-female2x4.png')

img = artist.generate( 'female', 3, 3, 3, 3, 3 )
img.save( '8bit-female3.png')
img.zoom(4).save( '8bit-female3x4.png')

img = artist.generate( 'female', 4, 4, 4, 4, 4 )
img.save( '8bit-female4.png')
img.zoom(4).save( '8bit-female4x4.png')
```

Voila!

![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-male1.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-male2.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-male3.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-male4.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-female1.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-female2.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-female3.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-female4.png)

4x

![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-male1x4.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-male2x4.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-male3x4.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-male4x4.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-female1x4.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-female2x4.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-female3x4.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-female4x4.png)



Let the artist do the painting, that is, let the computer fill in the numbers
with a random lottery.

``` ruby
img = artist.random( 'male' )
img.save( '8bit-male-random1.png')
img.zoom(4).save( '8bit-male-random1x4.png')

img = artist.random( 'female' )
img.save( '8bit-female-random1.png')
img.zoom(4).save( '8bit-female-random1x4.png')

img = artist.random
img.save( '8bit-random1.png')
img.zoom(4).save( '8bit-random1x4.png')

img = artist.random
img.save( '8bit-random2.png')
img.zoom(4).save( '8bit-random2x4.png')
```

Voila!

![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-male-random1.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-female-random1.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-random1.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-random2.png)

4x

![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-male-random1x4.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-female-random1x4.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-random1x4.png)
![](https://github.com/rubycoco/pixel/raw/master/pixelart-characters/i/8bit-random2x4.png)


That's it.



## Find Out More

### References

- Home :: [github.com/rubycoco/pixel/pixelart-characters](https://github.com/rubycoco/pixel/tree/master/pixelart-characters)
- Gem :: [pixelart-characters](https://rubygems.org/gems/pixelart-characters)
- Docs :: [pixelart-characters](http://rubydoc.info/gems/pixelart-characters)

