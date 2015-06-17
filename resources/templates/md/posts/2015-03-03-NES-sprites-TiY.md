{:title "Nintendo Sprites"
 :layout :post
 :tags ["TiY"]}


# NES | Sprites | Palettes

My final project at The Iron Yard will require me to pull and draw sprites from NES roms.
Last week I was able to get the sprites to draw.

So in an attempt to give back I thought I would share some of what I learned.

## Background

There are a few small pieces of info that you will need to know before we can get into pulling a sprite from a ROM and rendering it.

The NES is an 8-bit system.  The largest number a 8-bit value can hold is `decimal: 255` `hexadecimal: 0xFF` `binary: 11111111`

Games store their data in a multitude of ways, including compressing the graphics or keeping them in program data.  This makes some things tricky or impossible to do without actually running the ROM.

Being able to read hexideciamal and understanding binary math will help, but a quick trip to wikipedia should answer and questions you have.


## Storage
NES game data can be stored in two parts: PRG and CHR.

All games have PRG data.  This is the program data for the game and is stored in 16kb banks.

Most games also have CHR data, this is where the sprite information is kept and is stored in 8kb banks.
Not all games have CHR storage, Zelda, Mega Man, Metroid, all store their sprite data in PRG.  I will not cover how to obtain their sprite data.

For games that do use CHR its pretty trival to grab.
The ROM header will tell you how many banks of PRG and CHR there are.  To see the layout of the iNES header check out the [nesdev wiki](http://wiki.nesdev.com/w/index.php/INES).


## Drawing

Sprites are 8x8 or 8x16.  A 8x16 sprite is really just two 8x8 sprites.
It takes 16 bytes to make one sprite.  Each byte represents one row of the sprite, however since each pixel can have four values you need another byte.  8 x 2 = 16.
Each bit in the byte is a pixel.  We have to combine two bytes so that each pixel can be a value of 0-3.
Lets say byte 0 is `0x4D` (decimal 77) and we are combining it with byte 8 which is `0xC4` (decimal 196).


The two bytes are added together using binary addition:
```
  0  1  0  0  1  1  0  1
+ 1  1  0  0  0  1  0  0
------------------------
 10 11 00 00 01 11 00 01

  2  3  0  0  1  3  0  1
```

**Example:**

Given sprite data

```
[ 0x03, 0x0F, 0x1F, 0x1F, 0x1C, 0x24, 0x26, 0x66,
  0x00, 0x00, 0x00, 0x00, 0x1F, 0x3F, 0x3F, 0x7F]
```

```
|Bytes 0-7|  Binary  | Bytes 8-15|  Binary  |  Binary Addition  |
|---------|----------|-----------|----------|-------------------|
|0x03     | 00000011 |0x00       | 00000000 | 00000011          |
|0x0F     | 00001111 |0x00       | 00000000 | 00001111          |
|0x1F     | 00011111 |0x00       | 00000000 | 00011111          |
|0x1F     | 00011111 |0x00       | 00000000 | 00011111          |
|0x1C     | 00011100 |0x1F       | 00011111 | 00033322          |
|0x24     | 00100100 |0x3F       | 00111111 | 00322322          |
|0x26     | 00100110 |0x3F       | 00111111 | 00322332          |
|0x66     | 01100110 |0x7F       | 01111111 | 03322332          |
```

This is the top left of Mario's head.

![mario-head](/img/mario-head-top-left.png)

## Palettes

This is great but the CHR only stores the info necessary to draw the sprite.  I doesn't contain the color information.
For colors we need to obtain the palette data.  Sadly this could be anywhere in the PRG data or even generated at run time.  In order to obtain it you have to pull it from the PPU.  What I plan on doing is searching the PRG data for blocks of bytes that look like they may be palettes.

The NES palette only has 64 colors and palette data really just tells the PPU which color to pull by index.

Starting with the most simple thing that could work I walk the PRG data and collect bytes that are less than `0x40` [64].  If there are at least 4 contiguous bytes that are small enough I'll keep them as a candidate.


![mario-palette](/img/mario-palette.png)


As I work with the data I'm sure patterns will become apparent, and if they do I should be able to refactor the palette searching code to be better.



I also want to give a big thanks to [Brit](https://github.com/redline6561), the Ruby on Rails instructor.  Even though I'm not in his class he's been kind enough to sit down with me and work with me on this project.
