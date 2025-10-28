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
