# 1. rsa_oracle

## Description
> Can you abuse the oracle?
An attacker was able to intercept communications between a bank and a fintech company. They managed to get the message (ciphertext) and the password that was used to encrypt the message.
After some intensive reconassainance they found out that the bank has an oracle that was used to encrypt the password and can be found here nc titan.picoctf.net 60447. Decrypt the password and use it to decrypt the message. The oracle can decrypt anything except the password.

## Solution
First, I wanted to see what would happen if I directly tried to decrypt the password.enc file.
```bash
priya@DESKTOP-1TEEMJT:~$ nc titan.picoctf.net 60447
*****************************************
****************THE ORACLE***************
*****************************************
what should we do for you?
E --> encrypt D --> decrypt.
D
Enter text to decrypt: 4228273471152570993857755209040611143227336245190875847649142807501848960847851973658239485570030833999780269457000091948785164374915942471027917017922546
Lol, good try, can't decrypt that for you. Be creative and good luck

what should we do for you?
E --> encrypt D --> decrypt.
```
Obviously that did not work. So our goal is to somehow trick the oracle into giving us the answers. To do so, I took encrypted values of 1,2,3,4 to figure out value of N using the following Python code:
```Python
import math
data = [(49, 4374671741411819653095065203638363839705760144524191633605358134684143978321095859047126585649272872908765432040943055399247499744070371810470682366100689),
        (50, 4707619883686427763240856106433203231481313994680729548861877810439954027216515481620077982254465432294427487895036699854948548980054737181231034760249505),
        (51,  1998517197048216725617978890728205902760633363770165103499700157925986170022682604311921651991344892635565706489644418147980643978563559991322776155635395),
        (52, 3993239489061277327472930109138093827255646312769901312414509207541733524779884801267968848884701166599834406248783129646083261476137481855550108336137485),]
def Find_N(data, e=65537):
        a, b = data[0] #unpacking the tuple
        val_N = b - pow(a, e)
        for i,j in data[1:]:
            tmp = j - pow(i, e)
            val_N = math.gcd(tmp, val_N)
        return abs(val_N)
N=Find_N(data)
print("Value of N is",N)
```
The logic behind this is: (ciphertext)=m^e * (mod N) => c - m^e = N * k. And k is found using GCD method thereby finally leading us to N
```output
Value of N is 5507598452356422225755194020880876452588463543445995226287547479009566151786764261801368190219042978883834809435145954028371516656752643743433517325277971
```
Next, since RSA has a multiplicative property, I can multiply the value with any number like 2,3,4...etc and make the oracle decrypt it and simply divide that number to get the actual flag. Here, I will use x=10.
```Python
password= int(4228273471152570993857755209040611143227336245190875847649142807501848960847851973658239485570030833999780269457000091948785164374915942471027917017922546)   
x=10
encrypted= (password * pow(x, e, N)) % N
print("Modified value is",encrypted)
```
```output
Modified value is 2246069766871706352813845635896283550427369688733816925667491485440565630208989627729225212984783125979682554685302094776744105380218282706364056056744223
```
```bash
what should we do for you?
E --> encrypt D --> decrypt.
D
Enter text to decrypt: 2246069766871706352813845635896283550427369688733816925667491485440565630208989627729225212984783125979682554685302094776744105380218282706364056056744223
decrypted ciphertext as hex (c ^ d mod n): 3ebcbe23c3a
decrypted ciphertext: ëËâ<:

what should we do for you?
E --> encrypt D --> decrypt.
```
Now that I got the decrypted value of our fake password, I have to reverse it back to the one I require using this code:
```Python
# after checking the hex value in the oracle:
fake_hex = "3ebcbe23c3a"
p2 = int(fake_hex, 16)  #to conv to integer
inv_s = pow(x, -1, N)
m = (p2 * inv_s) % N

k = (N.bit_length() + 7) // 8
m_bytes = m.to_bytes(k, "big")

print("m:", m_bytes.rstrip(b'\x00').decode('utf-8'))
```
```output
m: da099
```
Finally, I used openssl command (mentioned in the hints) I decrypted the secret.enc with the retrieved `da099` as the password:
```bash
priya@DESKTOP-1TEEMJT:~$ openssl enc -aes-256-cbc -d -in secret.enc
enter AES-256-CBC decryption password:
*** WARNING : deprecated key derivation used.
Using -iter or -pbkdf2 would be better.
picoCTF{su((3ss_(r@ck1ng_r3@_da099d93}
```
The complete, final Python code used is:
```Python
import math

data = [(49, 4374671741411819653095065203638363839705760144524191633605358134684143978321095859047126585649272872908765432040943055399247499744070371810470682366100689),
        (50, 4707619883686427763240856106433203231481313994680729548861877810439954027216515481620077982254465432294427487895036699854948548980054737181231034760249505),
        (51,  1998517197048216725617978890728205902760633363770165103499700157925986170022682604311921651991344892635565706489644418147980643978563559991322776155635395),
        (52, 3993239489061277327472930109138093827255646312769901312414509207541733524779884801267968848884701166599834406248783129646083261476137481855550108336137485),]

def Find_N(data, e=65537):
        a, b = data[0] #unpacking the tuple
        val_N = b - pow(a, e)
        for i,j in data[1:]:
            tmp = j - pow(i, e)
            val_N = math.gcd(tmp, val_N)
        return abs(val_N)
N=Find_N(data)
print("Value of N is",N)

e=65537
password= int(4228273471152570993857755209040611143227336245190875847649142807501848960847851973658239485570030833999780269457000091948785164374915942471027917017922546)   
x=10
encrypted= (password * pow(x, e, N)) % N
print("Modified value is",encrypted)

# after checking the hex value in the oracle:
fake_hex = "3ebcbe23c3a"
p2 = int(fake_hex, 16)  #to conv to integer
inv_s = pow(x, -1, N)
m = (p2 * inv_s) % N

k = (N.bit_length() + 7) // 8
m_bytes = m.to_bytes(k, "big")

print("m", m_bytes.rstrip(b'\x00').decode('utf-8'))
```
## Flag
```
picoCTF{su((3ss_(r@ck1ng_r3@_da099d93}
```
## Concepts Learnt
- I learnt about cracking RSA encryption by getting various encrypted data and finding the value of N
- I learnt the method used in RSA encryption
- I learnt how to use Python to manipulate the data I have, to find the values required
## References
<https://www.cryptool.org/en/cto/rsa-step-by-step/>
***

# 2. Custom encryption

## Description
> Can you get sense of this code file and write the function that will decode the given encrypted file content.
Find the encrypted file here flag_info and code file might be good to analyze and get the flag.

## Solution
On analysing the given encryption Python code, I understood that it had this process:
* Two random numbers a, b to form a shared_key
* An xor function to encrypt the flag using "trudeau" as it's key
* Another encryption function that appends numbers to a list such that each letter in the xor-ed string has it's ASCII value multiplied with 311 * shared_key

With this knowledge, I was able to make a similar decryption program to find the flag since values of a,b and the cipher list were given in the txt file.

```Python3
import sys
from custom_encryption import generator,is_prime

def dynamic_xor_decrypt(plaintext,text_key): #apply encryption again to decrypt
    cipher_text = ""
    key_length = len(text_key)
    for i, char in enumerate(plaintext):
        key_char = text_key[i % key_length]
        encrypted_char = chr(ord(char) ^ ord(key_char))
        cipher_text += encrypted_char
    return cipher_text[::-1]

def decrypt(cipher, key):
    txt=[]
    for i in cipher:
        x=311*key
        y=round(i//x)
        txt.append(chr(y))
    return "".join(txt)

def test(a,b):
    p = 97
    g = 31
    if not is_prime(p) and not is_prime(g):
        print("Enter prime numbers")
        return
    print(f"a = {a}")
    print(f"b = {b}")
    u = generator(g, a, p)
    v = generator(g, b, p)
    key = generator(v, a, p) 
    b_key = generator(u, b, p)
    shared_key = None
    if key == b_key:
        shared_key = key
    else:
        print("Invalid key")
        return
    return shared_key

if __name__ == "__main__":  
    a=94
    b=29
    cipher=[260307, 491691, 491691, 2487378, 2516301, 0,
    1966764, 1879995, 1995687, 1214766, 0, 2400609, 607383,
    144615, 1966764, 0, 636306, 2487378, 28923, 1793226,
    694152, 780921, 173538, 173538, 491691, 173538, 751998,
    1475073, 925536, 1417227, 751998, 202461, 347076, 491691]
    shared_key=test(a,b)
    text=decrypt(cipher,shared_key)
    flag=dynamic_xor_decrypt(text,"trudeau")
    print(flag)
```
#### Output:
```bash
a = 94
b = 29
picoCTF{custom_d2cr0pt6d_751a22dc}
```

## Flag:
```
picoCTF{custom_d2cr0pt6d_751a22dc}
```
## Concepts Learnt:
I learnt about how to analyse a Python code and decrypt it to obtain the flag 

***

# 3. miniRSA

## Description
> Let's decrypt this: ciphertext? Something seems a bit small.

## Solution
ciphertext file has the following content:
```

N: 29331922499794985782735976045591164936683059380558950386560160105740343201513369939006307531165922708949619162698623675349030430859547825708994708321803705309459438099340427770580064400911431856656901982789948285309956111848686906152664473350940486507451771223435835260168971210087470894448460745593956840586530527915802541450092946574694809584880896601317519794442862977471129319781313161842056501715040555964011899589002863730868679527184420789010551475067862907739054966183120621407246398518098981106431219207697870293412176440482900183550467375190239898455201170831410460483829448603477361305838743852756938687673
e: 3

ciphertext (c): 2205316413931134031074603746928247799030155221252519872649649212867614751848436763801274360463406171277838056821437115883619169702963504606017565783537203207707757768473109845162808575425972525116337319108047893250549462147185741761825125 
```
From this, I understood that since the value of e is small, it would be easier to decode the RSA encryption than if value of e is large.
In RSA encryption, the ciphertext c=m^e/(mod(N)) where m is the original message as a number, e is exponent and N is a large number.
But if m^e < N then the N essentially doesnt have a role.
So the formula simplifies to c=m^e, in this case, c=m^3 ie., cube root of c = m

First, take the length of initial bit and then divide by 3 to find the initial length of the message and make a number out of that size using 1 <<. 
Using a loop, I try to continue guessing this number by squaring the number and checking how many times this number can fit into the original number using c//(x*x).
The loop ends if the number is greater than or equal to the previous iteration and finally bits is converted into bytes then to text.

```Python3
c=2205316413931134031074603746928247799030155221252519872649649212867614751848436763801274360463406171277838056821437115883619169702963504606017565783537203207707757768473109845162808575425972525116337319108047893250549462147185741761825125 
x = 1 << ((c.bit_length() + 2)//3)          
while True:
   y = (2*x + c//(x*x)) // 3
   if y >= x:
       m = x; break
   x = y
plaintext = x.to_bytes((x.bit_length()+7)//8, 'big')
print(plaintext.decode())
```

## Flag:
```
picoCTF{n33d_a_lArg3r_e_606ce004}
```
## Concepts Learnt:
I learnt how RSA encryptions can be weak if a small value of e is used. And how to decode them if that is the case. I also learnt about the Newtons method of finding cube root of large numbers with precision

## References
<https://www.programmersought.com/article/29736650877/>
***
