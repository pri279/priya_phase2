# Hide and Seek

## Description
> Sakamoto’s at it again with a game of hide and seek, but this time, it’s not with Shin or his daughter. An old friend hid some secret data in this image. Can you find it before the others do?

Hint:
Even in retirement, Sakamoto never loses at hide and seek. Maybe stegseek can help you keep up.
```
Username: Belegar
Password: Ironhammer
```

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
