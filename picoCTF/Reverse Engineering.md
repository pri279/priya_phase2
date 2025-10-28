# 1. GDB baby step 1

## Description
> Can you figure out what is in the eax register at the end of the main function? Put your answer in the picoCTF flag format: picoCTF{n} where n is the contents of the eax register in the decimal number base. If the answer was 0x11 your flag would be picoCTF{17}.
Disassemble this.

## Solution
First I opened the debugger0_a file with gdb and used disassemble main command to view the assembly instructions in main. 
<img width="956" height="696" alt="image" src="https://github.com/user-attachments/assets/b77b534e-64ab-48ad-b183-c93314ff40a0" />

Here, I was able to find the eax register and since the value was given in hex right next to it, I was able to convert it using print command.

<img width="277" height="98" alt="image" src="https://github.com/user-attachments/assets/3f3b5b77-5b9e-4271-83a0-b52889901a80" />

Enclosing this with picoCTF{} I was able to obtain the flag.

## Flag
```
picoCTF{549698}
```

## Concepts Learnt:
- Learnt how to use gdb for debugging and a few basic commands (from GDB Test Drive <https://play.picoctf.org/practice/challenge/273>

## References
<https://play.picoctf.org/practice/challenge/273>

***

# 2. ARMssembly 1

***

# 3. vault-door-3

## Description
> This vault uses for-loops and byte arrays. The source code for this vault is here: VaultDoor3.java
```
import java.util.*;

class VaultDoor3 {
    public static void main(String args[]) {
        VaultDoor3 vaultDoor = new VaultDoor3();
        Scanner scanner = new Scanner(System.in);
        System.out.print("Enter vault password: ");
        String userInput = scanner.next();
	String input = userInput.substring("picoCTF{".length(),userInput.length()-1);
	if (vaultDoor.checkPassword(input)) {
	    System.out.println("Access granted.");
	} else {
	    System.out.println("Access denied!");
        }
    }

    // Our security monitoring team has noticed some intrusions on some of the
    // less secure doors. Dr. Evil has asked me specifically to build a stronger
    // vault door to protect his Doomsday plans. I just *know* this door will
    // keep all of those nosy agents out of our business. Mwa ha!
    //
    // -Minion #2671
    public boolean checkPassword(String password) {
        if (password.length() != 32) {
            return false;
        }
        char[] buffer = new char[32];
        int i;
        for (i=0; i<8; i++) {
            buffer[i] = password.charAt(i);
        }
        for (; i<16; i++) {
            buffer[i] = password.charAt(23-i);
        }
        for (; i<32; i+=2) {
            buffer[i] = password.charAt(46-i);
        }
        for (i=31; i>=17; i-=2) {
            buffer[i] = password.charAt(i);
        }
        String s = new String(buffer);
        return s.equals("jU5t_a_sna_3lpm18gb41_u_4_mfr340");
    }
}
```

## Solution
On analyzing the Java code given, I understood that the final string given was converted from the original password string using the for loops. On reversing those for loops one by one, I was able to obtain the flag.
The first 8 characters remained the same for the string and the flag. The characters at index 8 to 15 were reversed in order. The characters at index 16 to 32 were reversed at every even index. And finally the odd indices remained the same.  

## Flag:
```picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_1fb380}```

## Concepts Learnt:
I learnt how to use reverse engineering to analyse and understand a particular code to find the flag. 

***
