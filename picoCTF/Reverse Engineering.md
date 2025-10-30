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

## Description
> For what argument does this program print `win` with variables 85, 6 and 3? File: chall_1.S Flag format: picoCTF{XXXXXXXX} -> (hex, lowercase, no 0x, and 32 bits. ex. 5614267 would be picoCTF{0055aabb})

## Solution
Given is an ARM v-8 assembly code which basically does a few mathematical steps and is a simple password checking program
```
	.arch armv8-a
	.file	"chall_1.c"
	.text
	.align	2
	.global	func
	.type	func, %function
func:
	sub	sp, sp, #32
	str	w0, [sp, 12]
	mov	w0, 85
	str	w0, [sp, 16]
	mov	w0, 6
	str	w0, [sp, 20]
	mov	w0, 3
	str	w0, [sp, 24]
	ldr	w0, [sp, 20]
	ldr	w1, [sp, 16]
	lsl	w0, w1, w0
	str	w0, [sp, 28]
	ldr	w1, [sp, 28]
	ldr	w0, [sp, 24]
	sdiv	w0, w1, w0
	str	w0, [sp, 28]
	ldr	w1, [sp, 28]
	ldr	w0, [sp, 12]
	sub	w0, w1, w0
	str	w0, [sp, 28]
	ldr	w0, [sp, 28]
	add	sp, sp, 32
	ret
	.size	func, .-func
	.section	.rodata
	.align	3
.LC0:
	.string	"You win!"
	.align	3
.LC1:
	.string	"You Lose :("
	.text
	.align	2
	.global	main
	.type	main, %function
main:
	stp	x29, x30, [sp, -48]!
	add	x29, sp, 0
	str	w0, [x29, 28]
	str	x1, [x29, 16]
	ldr	x0, [x29, 16]
	add	x0, x0, 8
	ldr	x0, [x0]
	bl	atoi
	str	w0, [x29, 44]
	ldr	w0, [x29, 44]
	bl	func
	cmp	w0, 0
	bne	.L4
	adrp	x0, .LC0
	add	x0, x0, :lo12:.LC0
	bl	puts
	b	.L6
.L4:
	adrp	x0, .LC1
	add	x0, x0, :lo12:.LC1
	bl	puts
.L6:
	nop
	ldp	x29, x30, [sp], 48
	ret
	.size	main, .-main
	.ident	"GCC: (Ubuntu/Linaro 7.5.0-3ubuntu1~18.04) 7.5.0"
	.section	.note.GNU-stack,"",@progbits
```
In the function, it stores user input into the stack, then adds 85, 6 and 3 in positions 12,16,20,and 24.
Next it takes 6 and 85 as w0 and w1 and operates left shift on it (85 x 64 = 5440) and puts 5440 into 28th position.
Using sdiv command, it divides 5440/3 = 1813 and puts it in 28th position.
Next it subtracts 1813-(user input) and stores it in 28th position and returns that value.

Now in the main function, it stores the user input and converts it from string to number using atoi, calls the function explained above and finally compares if its zero. If it is, it goes to .L4 which says "You win!"
So basically the 1813-(user input) must be 0 for the flag. Hence, user input must be 1813.
Converting to 32 bit hexadecimal, 0x00000715.

## Flag
```
picoCTF{00000715}
```

## Concepts Learnt:
- LSL : rd, rn, rm => rd = rn << rm
- SDIV : rd, rn, rm => rd = rn ÷ rm ;  Signed Divide divides a 32-bit signed integer register value by a 32-bit signed integer register value, and writes the result to the destination register.
- SUB{S} : rd, rn, op2 => rd = rn - op2
- atoi : ascii to integer

## References
<https://courses.cs.washington.edu/courses/cse469/19wi/arm64.pdf>
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
