# Hide and Seek

## Description
> Sakamoto’s at it again with a game of hide and seek, but this time, it’s not with Shin or his daughter. An old friend hid some secret data in this image. Can you find it before the others do? \
Hint:
Even in retirement, Sakamoto never loses at hide and seek. Maybe stegseek can help you keep up.

## Solution
Since it was clearly mentioned in the description to try using stegseek, I used the command \
`stegseekstegseek sakamoto.jpg rockyou.txt` \
and was able to obtain the flag.

```bash
priya:~$ stegseek sakamoto.jpg rockyou.txt
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Found passphrase: "iloveyou1"
[i] Original filename: "flag.txt".
[i] Extracting to "sakamoto.jpg.out".

priya@DESKTOP-1TEEMJT:~$ cat sakamoto.jpg.out
nite{h1d3_4nd_s33k_but_w1th_st3g_sdfu9s8}
```

## Flag:
```nite{h1d3_4nd_s33k_but_w1th_st3g_sdfu9s8}```

## Concepts Learnt:
I learnt how to use stegseek tool to extract hidden data in a image file.
***

# Nutrela Chunks

## Description
> One of my favorite foods is soya chunks. But as I was enjoying some Nutrela today, I noticed a few chunks weren’t quite right. Seems like something’s off with their structure. Could you help me fix these broken chunks so I can enjoy my meal again?

## Solution
The description hints at "chunks" so I tried to open the corrupted png file using a Hex Editor <https://hexed.it/> \
I also used the tool pngcheck and saw that it wasn't even recognised as a PNG file which meant it's PNG header was incorrect, so I fixed that.
Similarly, I used both hexed.it and pngcheck to fix other chunks like IHDR, IDAT and IEND.
Finally when the tool said there were no more errors, I opened the file and obtained the flag.

```bash
priya:~$ pngcheck -v nutrela.png
File: nutrela.png (538662 bytes)
  this is neither a PNG or JNG image nor a MNG stream
ERRORS DETECTED in nutrela.png
priya:~$ pngcheck -v nutrela2.png
File: nutrela2.png (538662 bytes)
  chunk ihdr at offset 0x0000c, length 13:  first chunk must be IHDR
ERRORS DETECTED in nutrela2.png
priya:~$ pngcheck -v nutrela3.png
File: nutrela3.png (538662 bytes)
  chunk IHDR at offset 0x0000c, length 13
    1000 x 1000 image, 24-bit RGB, non-interlaced
  chunk idat at offset 0x00025, length 538605:  illegal reserved-bit-set chunk
ERRORS DETECTED in nutrela3.png
priya:~$ pngcheck -v nutrela3.png
File: nutrela3.png (538662 bytes)
  chunk IHDR at offset 0x0000c, length 13
    1000 x 1000 image, 24-bit RGB, non-interlaced
  chunk IDAT at offset 0x00025, length 538605
    zlib: deflated, 32K window, default compression
  chunk IEND at offset 0x8381e, length 0
No errors detected in nutrela3.png (3 chunks, 82.0% compression).
```
<img width="607" height="612" alt="image" src="https://github.com/user-attachments/assets/b0d8076c-dbba-46c7-a58c-c237fc4c5e9f"/>

## Flag:
```nite{n0w_y0u_kn0w_ab0ut_PNG_chunk5}```

## Concepts Learnt:
I learnt about PNG chunks and how a file could be corrupted if the chunks are incorrect. I learnt how to fix the chunks using tools like hexed.it and pngcheck.

## References:
<https://medium.com/@0xwan/png-structure-for-beginner-8363ce2a9f73> \
<https://www.libpng.org/pub/png/spec/1.2/PNG-Chunks.html#:~:text=The%20IDAT%20chunk%20contains%20the%20output%20datastream%20of%20the%20compression,of%20all%20the%20IDAT%20chunks>
***
