# pranav's FindMySecret - writeup

Step 1: Execute the program to look for strings 

```
>FindMySecret.exe
Enter the secret number: 3
Nope, you have not yet found the secret number.
```

Step 2: Find the main function

```
>r2 FindMySecret.exe
[0x004014a0]> aaaa
[x] Analyze all flags starting with sym. and entry0 (aa)
[x] Analyze function calls (aac)
[x] Analyze len bytes of instructions for references (aar)
[x] Check for objc references
[x] Check for vtables
[x] Type matching analysis for all functions (aaft)
[x] Propagate noreturn information
[x] Use -AA or aaaa to perform additional experimental analysis.
[x] Finding function preludes
[x] Enable constraint types analysis for variables
[0x004014a0]> afl
                ...
0x004017e4   16 318          main
                ...
```

Step 3: Explore the main function

```
[0x004014a0]> pdf @main
|      .--> 0x00401859      c70424c84040.  mov dword [esp], str.Enter_the_secret_number ; [0x4040c8:4]=0x65746e45 ; "Enter the secret number" ; const char *format
|      :|   0x00401860      e897100000     call sub.msvcrt.dll_printf  ; int printf(const char *format)
|      :|   0x00401865      8d45ec         lea eax, [var_14h]
|      :|   0x00401868      89442404       mov dword [var_2ch], eax
|      :|   0x0040186c      c70424e04040.  mov dword [esp], 0x4040e0   ; [0x4040e0:4]=0x49006425 ; "%d" ; const char *format
|      :|   0x00401873      e86c100000     call sub.msvcrt.dll_scanf   ; int scanf(const char *format)
|      :|   0x00401878      8b45ec         mov eax, dword [var_14h]
|      :|   0x0040187b      3d0f270000     cmp eax, 0x270f
|     ,===< 0x00401880      7f07           jg 0x401889
|     |:|   0x00401882      8b45ec         mov eax, dword [var_14h]
|     |:|   0x00401885      85c0           test eax, eax
|    ,====< 0x00401887      7f0e           jg 0x401897
|    ||:|   ; CODE XREF from main @ 0x401880
|    |`---> 0x00401889      c70424e34040.  mov dword [esp], str.Invalid_Range... ; [0x4040e3:4]=0x61766e49 ; "Invalid Range...\n" ; const char *s
|    | :|   0x00401890      e857100000     call sub.msvcrt.dll_puts    ; int puts(const char *s)
|    |,===< 0x00401895      eb5f           jmp 0x4018f6
|    ||:|   ; CODE XREF from main @ 0x401887
|    `----> 0x00401897      8b45ec         mov eax, dword [var_14h]
|     |:|   0x0040189a      668945f2       mov word [var_eh], ax
|     |:|   0x0040189e      a13c304000     mov eax, dword [0x40303c]   ; [0x40303c:4]=0x4016a4
|     |:|   0x004018a3      83c064         add eax, 0x64               ; 100
```

It's reassuring to see that the string "Enter the secret number" is there. The program will check to see if the number is greater than 9999. If it is greater, then it will jump to the print statement "Invalid Range". Also, there is another function at (0x4016a4 + 100) which is 0x401708.

Step 4: Explore functions that are related to the main function (Ghidra)

I used Ghidra here to look at the decompiled version of the assembly code.

![image](https://user-images.githubusercontent.com/81070073/121983766-b7346080-cd46-11eb-9359-c64020600b4f.png)

There are two if statements. The first if statement initializes, multiplies the secret number by 10000, and stores the result @ 004063e8. The second if statement compares the input number with the secret number.

Step 5: Find out what the secret number is. (Ollydbg)

I opened the debugger Ollydbg, ran the program, and Ctrl + G to navigate to the address 004063e8.

![image](https://user-images.githubusercontent.com/81070073/121984361-af28f080-cd47-11eb-90b3-1944c95d4ecd.png)

It appears to be gibberish only because it's in ASCII. We need to change this to a 64-bit double.

![image](https://user-images.githubusercontent.com/81070073/121984486-e6979d00-cd47-11eb-9d7f-623f1b25bee2.png)

![image](https://user-images.githubusercontent.com/81070073/121984534-fd3df400-cd47-11eb-9f6b-b3ac52950362.png)








