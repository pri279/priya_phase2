# 1. Trivial Flag Transfer Protocol

## Description
> Figure out how they moved the flag.

## Solution
First, I opened tftp.pcapng using Wireshark and analysed the various transfers that took place. I saw that there was a mention of instructions.txt and other files so I used the Export Objects option and got five files.
On opening the first file, instructions.txt, I decoded the text using ROT13 and got the string:
"TFTPDOESNTENCRYPTOURTRAFFICSOWEMUSTDISGUISEOURFLAGTRANSFER.FIGUREOUTAWAYTOHIDETHEFLAGANDIWILLCHECKBACKFORTHEPLAN"
Since it said "check the plan", I checked the file called plan and decoded that too using ROT13:
"IUSEDTHEPROGRAMANDHIDITWITH-DUEDILIGENCE.CHECKOUTTHEPHOTOS". 
Then I tried to install the program.deb file using my terminal:

```bash
priya@DESKTOP-1TEEMJT:~$ sudo apt-get install ./program.deb
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Note, selecting 'steghide' instead of './program.deb'
Some packages could not be installed. This may mean that you have
requested an impossible situation or if you are using the unstable
distribution that some required packages have not yet been created
or been moved out of Incoming.
The following information may help to resolve the situation:

The following packages have unmet dependencies:
 steghide : Depends: libjpeg62-turbo (>= 1:1.3.1) but it is not installable
E: Unable to correct problems, you have held broken packages.
```
In the downloading section, it says "Note, selecting 'steghide' instead of './program.deb'" which hints that I need to use steghide tool to decode the data in the pictures. After installing it, I checked the 1st and 2nd picture but to no avail. On checking the third picture, the console wrote into a file called flag.txt and hence I was able to obtain the flag.

```bash
priya@DESKTOP-1TEEMJT:~$ steghide extract  -sf ~/picture1.bmp -p "DUEDILIGENCE"
steghide: could not extract any data with that passphrase!
priya@DESKTOP-1TEEMJT:~$ steghide extract  -sf ~/picture2.bmp -p "DUEDILIGENCE"
steghide: could not extract any data with that passphrase!
priya@DESKTOP-1TEEMJT:~$ steghide extract  -sf ~/picture3.bmp -p "DUEDILIGENCE"
wrote extracted data to "flag.txt".
priya@DESKTOP-1TEEMJT:~$ cat ~/flag.txt
picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
```

## Flag:
```
picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
```
## Concepts Learnt:
I learnt about using steghide as a tool to find hidden data in jpeg,bmp and wav files.

## Resources
https://steghide.sourceforge.net/documentation/manual.pdf
***

# 2. tunn3l v1s10n

***

# 3. m00nwalk

## Description
> Decode this message from the moon.

## Solution
The message.wav file was an audio file with SSTV encoding which I understood from the description that said the message was sent from the moon. On researching a little bit, I found an online decoder <https://sstv-decoder.mathieurenaud.fr/> using which I was able to decode the file into an image which contained the flag. 

<img width="320" height="256" alt="image" src="https://github.com/user-attachments/assets/5b0d97c4-cfc9-43d7-a5db-e52d72f9fa7b" />

## Flag:
```
picoCTF{beep_boop_im_in_space}
```
## Concepts Learnt:
I learnt about SSTV (Slow-scan TV) and tools to decode an SSTV encoded file.

## Resources
https://trevorsaudi.medium.com/razictf-cliche-steganography-8b9de1bc8f25

***


