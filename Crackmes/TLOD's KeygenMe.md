# TLOD's KeygenMe - writeup

Step 1: Open the program

```
>KeygenMe.exe

Input name: name
Input password: 3
Wrong password!

```

Step 2: Look for the main function

I saw the string "Input name" earlier, so that's the string I used to search for the main function on Ghidra.

![image](https://user-images.githubusercontent.com/81070073/122006286-7f8bdf80-cd6b-11eb-9c98-d7f6c8683728.png)

The main function seems to be "FUN_004012a0".

Step 3: Understand the main function

The main function has a lot of if statements and the conditions are dependent on the length of input. It will be easier to explain using the decompiled version after the first condition.

**If length of input == 1:**

![image](https://user-images.githubusercontent.com/81070073/122007324-ad255880-cd6c-11eb-8b2e-81680e140594.png)

        0040130e 83 f8 01        CMP        EAX,0x1               EAX is the length of input and will be compared with the value of 1
                                  ...
        0040131e 0f be 30        MOVSX      ESI,byte ptr [EAX]    ESI will store the password and it is equal to the input[0]
        
**if length of input == 2:**

![image](https://user-images.githubusercontent.com/81070073/122007723-186f2a80-cd6d-11eb-9f4c-caef1e01107a.png)

    if (local_1c == 2) {                              If input length == 2,
      pppuVar4 = local_2c;
      if (0xf < local_18) {
        pppuVar4 = local_2c[0];
      }
      iVar8 = (int)*(char *)((int)pppuVar4 + 1);      iVar8 = input[1]
      iVar2 = (int)*(char *)pppuVar4;                 iVar2 = input[0]
      LAB_0040161d:      iVar8 = iVar8 * iVar2;       then password == input[1] * input[0]

**if length of input == 3:**

![image](https://user-images.githubusercontent.com/81070073/122008479-f5914600-cd6d-11eb-8b1f-eff3234f5fa9.png)

      if (local_1c == 3) {                                                        If input length == 3,
        pppuVar4 = local_2c;
        if (0xf < local_18) {
          pppuVar4 = local_2c[0];
        }
        pppuVar5 = local_2c;
        if (0xf < local_18) {
          pppuVar5 = local_2c[0];
        }
        iVar8 = ((int)*(char *)((int)pppuVar5 + 1) + (int)*(char *)pppuVar4) *  
                (int)*(char *)((int)pppuVar5 + 2) - (int)*(char *)pppuVar5;       iVar8 = (input[1] + input[0]) * input[2] - input[0]
        iVar2 = (int)*(char *)((int)pppuVar5 + 2);                                iVar2 = input[2]     
        goto LAB_0040161d                                                         Jump back to the iVar8 = iVar8 * iVar2 statement, so password = (input[1] + input[0] * input[2] - input[0]) * input[2]

There are more conditional statements and it uses the same logic, so I will skip those explanations. In order to crack this program, I don't really need to know all of the possible outcomes, I just need to abuse one.

Step 4: Crack the program

If I type the name to be "a", then I expect the password to be its corresponding ascii value which is 97.
If I type the name to be "ab", then I expect the password to be the product of ascii value of a and ascii value of b which is 97 * 98 = 9506.
If I type the name to be "abc", then the password should be ((98+97)*99-97) * 99 = 1901592

![image](https://user-images.githubusercontent.com/81070073/122011340-d3e58e00-cd70-11eb-9da3-3463df22f5fe.png)
