# 1. IQ Test

## Description
> let your input x = 30478191278.
wrap your answer with nite{ } for the flag.
As an example, entering x = 34359738368 gives (y0, ..., y11), so the flag would be nite{010000000011}.

<img width="140" height="210" alt="iqtest" src="https://github.com/user-attachments/assets/b3c1b598-7275-4964-8799-d0eb88e87545" />


## Solution
First I converted the given value of x=30478191278 into binary and got 11100011000101001000100101010101110. Since there are 36 inputs, I added a 0 to the starting of the number and got 011100011000101001000100101010101110. On solving the logic gates, I was able to retrieve the final output.

![WhatsApp Image 2025-10-25 at 17 34 51_abeed860](https://github.com/user-attachments/assets/e2bb3a22-1be6-4453-b7d3-4983bac9d1bb)

## Flag:
```
nite{100010011000}
```
***

# 2. I like Logic

## Description
> i like logic and i like files, apparently, they have something in common, what should my next step be.

## Solution
First I searched up how I can open .sal files and found a software called logic2 and installed it. This matched with the challenge description which told me I was in the right direction.
I opened the challenge.sal file given using Logic and saw that there was a square wave on the 3rd channel.
I converted the data to ASCII and got a long string of text.
<img width="1481" height="944" alt="image" src="https://github.com/user-attachments/assets/8c7a71c1-c931-4f8c-ae28-7795aaafb969" />

On zooming in and searching '{', I went to one part of the string that said:
```
ird."\r\n\r\nFCSC{b1dee4eeadf6c4e60aeb142b0b486344e64b12b40d1046de95c89ba5e23a9925}\r\n\r\n"A pterodactyl."\r\n\r\n"That's what HE says.  He
```
From here I was able to get the flag.

## Flag
```
FCSC{b1dee4eeadf6c4e60aeb142b0b486344e64b12b40d1046de95c89ba5e23a9925}
```

## Concepts Learnt
I understood how to access .sal files and also how to analyse square wave signals given.
It's full form is Security Audit Log (SAL)

## References 
<https://file.org/extension/sal#salealelogic2captureformat>

***

# 3. Bare Metal Alchemist

## Description
> my friend recommended me this anime but i think i've heard a wrong name.

## Solution
Since the challenge was a play on the anime "Fullmetal Alchemist, I searched what bare metal programming was and its relation to .elf files. 
I found that it was code that interacts directly with hardware similar to ARM v8 that I've worked with once before.
I checked how to open and view .elf files and installed it using `sudo apt install binutils-avr avr-libc`. 
I also checked the file type of the firmware.elf file to confirm I was on the right track and it showed:
```bash
priya@DESKTOP-1TEEMJT:~$ file firmware.elf
firmware.elf: ELF 32-bit LSB executable, Atmel AVR 8-bit, version 1 (SYSV), statically linked, with debug_info, not stripped
```
Then using the command, `priya@DESKTOP-1TEEMJT:~$ avr-objdump -d firmware.elf`, I was able to disassemble the code and get it displayed on the terminal:
<img width="1301" height="712" alt="image" src="https://github.com/user-attachments/assets/fcd23189-3d28-4cae-8f12-c64ddc8f827e" />
On analysing this, I found a few parts that seemed useful like usage of eor which meant it was decrypting xor 
```bash
 220:   95 ea           ldi     r25, 0xA5       ; 165
 222:   b9 2e           mov     r11, r25
 224:   20 e0           ldi     r18, 0x00       ; 0
```
It loads 0xA5 into register 25. Then reads from address 0x68 in:
```bash
 238:   88 e6           ldi     r24, 0x68       ; 104
 23a:   e8 2e           mov     r14, r24
 23c:   80 e0           ldi     r24, 0x00       ; 0
 23e:   f8 2e           mov     r15, r24
 240:   00 e0           ldi     r16, 0x00       ; 0
 242:   f7 01           movw    r30, r14
 244:   84 91           lpm     r24, Z
 ...
 24a:   e8 2f           mov     r30, r24
 24c:   eb 25           eor     r30, r11
 24e:   85 3a           cpi     r24, 0xA5       ; 165
 250:   41 f0           breq    .+16            ; 0x262 <main+0xec>
```
At 24c, it XORs r30 with 0xA5 and uses cpi to compare. Finally, it checks whether they're equal and breaks out of the loop. 
From this, I understood that it's taking data from address 0x68 so I used a Python code to decrypt this with the key: 0xA5

```Python
data=[0xf1, 0xe3, 0xe6, 0xe6, 0xf1, 0xe3, 0xde, 0xf1, 0xcd, 0x94, 0xd6, 0xfa,
    0x94, 0xd6, 0xfa, 0xd6, 0xca, 0xc8, 0x96, 0xfa, 0xd6, 0x94, 0xc8, 0xd5,
    0xc9, 0x96, 0xfa, 0x91, 0xd7, 0xc1, 0xd0, 0x94, 0xcb, 0xca, 0xfa, 0xc3,
    0x94, 0xd7, 0xc8, 0xd2, 0x91, 0xd7, 0xc0, 0xd8]
key = 0xA5
#xor decode
decoded = []
for i in data:
    decoded.append(i ^ key)
#conv to bytes
new_decode= bytes(decoded)
print(new_decode)
```
## Flag
```
TFCCTF{Th1s_1s_som3_s1mpl3_4rdu1no_f1rmw4re}
```
## Concepts Learnt
I learnt about bare metal programming languages and how you can use them to work directly on hardware
Different commands in it:
  - ldi : to load values into registers
  - mov : to move values
  - eor : stands for XOR (exclusive OR)
  - cpi : to compare
  - breq : breaks if its equal

## References
<https://maldus512.medium.com/bare-metal-programming-on-an-stm32f103-3a0f4e50ca29>
***


