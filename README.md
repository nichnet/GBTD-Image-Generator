# GBTD Sprite Encoder/Decoder

This tool generates hexadecimal sprite data for GBDK. Use it to encode image files as hexidecimal data and decode hexadecimal data generated by GBTD into PNG image files.

</br>

## Explanation
There is a full explanation on my blog, [here](https://www.github.com/nichnet/gbtd-sprite).

GBDK interprets sprite data generated by GBTD by assigning every pixel of a sprite image, 2 bits. These 2 bits determine the index for the color used in the assigned color palette. 

</br>

The color is determined like so:
|Bit 1|Bit 2|Color Palette Index|DMG1 Palette RGB|
|:--:|:--:|:--:|:--:|
|0|0|0|255, 255, 255|
|1|0|1|192, 192, 192|
|0|1|2|128, 128, 128|
|1|1|3|0, 0, 0|

</br>

The data for these sprites is represented in the form of an array containing hexadecimal values (a hex value contains 8 bits - 1 byte). Thus, a byte represents a single row of 8 pixels. As there are 4 colors in the palette 2 bytes are required.

For example the data array may contain: `0xBE, 0xD2,...`, which in binary and finally as a color index, is interpreted as:
|<b>B1</b>|<b>0xBE</b>|1|0|1|1|1|1|1|0|
|:--|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|<b>B2</b>|<b>0xD2</b>|1|1|0|1|0|0|1|0|
|<b>Color Palette Index</b>|<b>0-3</b>|3|2|1|3|1|1|3|0|

As mentioned above, this is <u>a single row of 8 pixels</u> and in this case, the colors are <i>`black, dark, light, black, etc...`</i> (in a standard GameBoy color palette).

</br> 
Every 8x8 pixels (16 bytes) is a `quadrant` .

|Width|Height|Pixels|Quadrants|
|:--:|:--:|:--:|:--:|
|8|8|64|1|
|8|16|128|2|
|16|16|256|4|
|32|32|1024|16|

</br>

The quadrants are organised in the following order:

</br>
8x8 (1 quadrant)

|0|
|-|

</br>
8x16 (2 quadrants)

|0|
|-|
|1|

</br>
16x16 (4 quadrants)

|0|2|
|-|-|
|1|3|

</br>
32x32 (16 quadrants)

|0|2|8|10|
|-|-|-|-|
|1|3|9|11|
|4|6|12|14|
|5|7|13|15|

</br>

## Usage

### Example Data

The tool comes packed with some example data. You can generate the image files from this data by running the following, and supplying one of the keys names below.
```python
py gbtdimg.py -d [example data key]
```
|Key|Name|Width|Height|Result|
|--|--|--|--|:--:|
|t88|Heart|8|8|![](https://raw.githubusercontent.com/nichnet/GBTD-Image-Generator/main/export/Heart.png)|
|t816|Sword|8|16|![](https://raw.githubusercontent.com/nichnet/GBTD-Image-Generator/main/export/Sword.png)|
|t1616|Link|16|16|![](https://raw.githubusercontent.com/nichnet/GBTD-Image-Generator/main/export/Link.png)|
|t3232|Red|32|32|![](https://raw.githubusercontent.com/nichnet/GBTD-Image-Generator/main/export/Red.png)|
</br>

### Decoding Hexidecimal Data to Image Files (PNG)

```python
py gbtdimg.py -d [exported image name] [data]

#comma seperated or space seperated values is okay.
#py gbtdimg.py -d MySprite 0x6C,0x6C,0xFE,0x92,0xD6,0xAA,0xC6,0xBA,0x6C,0x54,0x38,0x28,0x10,0x10,0x00,0x00
#py gbtdimg.py -d MySprite 0x6C 0x6C 0xFE 0x92 0xD6 0xAA 0xC6 0xBA 0x6C 0x54 0x38 0x28 0x10 0x10 0x00 0x00
```

The image will be generated inside an `export` folder.

</br>

### Encoding Images as Hexidecimal Data

<b>Images to encode should only be 8x8, 8x16, 16x16, or 32x32 in size, and contain the RGB values mentioned previously above.</b>

```python
py gbtdimg.py -e [image path] [label name] 
#py gbtdimg.py -e path/to/heart.png MyHeartLabel 
```

The result is a C source and header file containing the hex data, e.g.:

```
0x6C,0x6C,0xFE,0x92,0xD6,0xAA,0xC6,0xBA,
0x6C,0x54,0x38,0x28,0x10,0x10,0x00,0x00
```

</br>

## Issues and Concerns
<i>Strangely</i>, GBDK reads the quadrants (mentioned above) in an odd order.

GBTD does have export compression options, so perhaps the ordering is related to that. 

Regardless, for easier interpretation, I would expect uncompressed data to be ordered horiztonally and then vertically like this:

</br>
32x32 (16 quadrants)

|0|1|2|3|
|-|-|-|-|
|4|5|6|7|
|8|9|10|11|
|12|13|14|15|

</br>

As a result there is some [spaghetti code](https://github.com/nichnet/GBTD-Image-Generator/blob/main/encoder.py#L42) which organises the data appropriately for encoding and decoding until I research into this more.

</br>

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

<br/>

## License
[GNU General Public License v3.0](https://choosealicense.com/licenses/gpl-3.0/)
