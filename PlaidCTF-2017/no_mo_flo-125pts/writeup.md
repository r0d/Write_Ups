### no_mo_flo - PlaidCTF 2017 - 125 points - Reverse Engineering 
**Description - Can you go with the flow?**  
**Files: no_flo**

*Solved by r0d & adambomb* 

***

Running the binary we see it immediately expects user input. Anything less than 32 characters gives us a "short input" response, otherwise we are told  "You aint goin with the flow....". 

Jumping into IDA we see in main() that there is in fact a 32 character read followed by a 16 iteration loop. The loop creates two arrays, one containing the even characters and the other containing the odd characters. 

After the arrays are made, a user defined signal is created that jumps to a switch statement. Then the odd array is then passed to a function that presumably checks the characters and returns either a 0 or 1. Looking further down we see that we want the function to return a 1 to pass the first check. Finally, the even array is then passed to a different checking function which we also want to return a 1. 

Ok, so lets jump into the first check. We see that it is initializing r8d to 1 and then jumping through 22 different checks on individual characters. Since the array only has 16 characters we know it can't pass all of them. Looking closer, we see it checks some characters multiple times, but it only needs to pass the ones that don't set r8d to 0. Pulling the checked values out and reversing the operations we get the following string: 

P-T-{-0-f-0-_-0-l-k-_-h-h-l-_-0-

Taking the flag format into account, we can reasonably assume some of the characters: 

PCTF{-0-f-0-_-0-l-k-_-h-h-l-_-0}

So now lets look at the function checking the even array. This is a bit more convoluted. Like the first function, it is stepping through the array looking at some characters multiple times. However, instead of simply checking the character, it throws the previously defined signal which calls the checking function, which is a switch statement. Based on the above string, we know that first two characters should evaluate to C and F respectively. Using GDB, we see that when the first and second item in the array are evaluated under case 5 of the switch statement they are compared against a C and and F. So we step through the entire array and fill in the characters evaluated by case 5 to get the following string: 

PCTF{n0_fl0-_-0-l-k-_ah_h3ll_n0} 

The remaining characters are evaluated with a case 1 and a case 3. Stepping through one last time we arrive at the flag: 

PCTF{n0_fl0?_m0_like_ah_h3ll_n0}  
