# Antipindoc's Basic Password Checker - Writeup

Opening the file using Ghidra and navigating to the first unnamed "FUN" functions.

```
  if (in_stack_fffffff0 != 0x281ed70) {
    pcVar1 = "Wrong password.  HAHA!!!";
  }
```

"Wrong password" will only be printed out when the input doesn't equal to hexadecimal 0x281ed70 which is 42069360 in decimal. Therefore, the password is 42069360.

![image](https://user-images.githubusercontent.com/81070073/121837365-3fa0fb80-cc8a-11eb-884c-33c3b3e0a589.png)

```
>TryToCrackThatPassword.exe
Enter your Activation Key to access the program: 42069360
nice! Password is correct.
```
