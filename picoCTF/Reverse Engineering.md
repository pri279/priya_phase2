# 1. GDB baby step 1

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
