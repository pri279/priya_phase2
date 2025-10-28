# 1. rsa_oracle

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

***
