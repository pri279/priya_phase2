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

***


