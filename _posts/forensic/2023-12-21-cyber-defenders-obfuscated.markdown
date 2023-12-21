---
layout: post
title:  "EN | 'Obfuscated by Josh Stroschein' from cyberdefenders.org"
date:   2023-12-21 09:00:02 +0300
categories: reverse, forensic
---
![Zero](/assets/obf_0.jpg)

Welcome folks, I recently came across a very interesting and useful website 'cyberdefenders.org'. And specifically in this post I want to solve a task called 'obfuscated' by Josh Stroschein, which contains a Malicious Document.  
So, less words, let's get started.

>Q1: What is the sha256 hash of the doc file?

You can use lots of tools, but i think the fastest and easiest way to get any type hash of your malware sample is to upload the file to virus total.  
Once the file has been uploaded, navigate to Details, and here we are.
![1](/assets/obf_1.jpg)

>Q2: Multiple streams contain macros in this document. Provide the number of lowest one.

If we look closely at the details of this task, we can find a very useful and powerful tool called "oledump" by Didier Stevens. After installation type 'oledump.py filename' to show all streams. In this file we have 20 streams, and some marks - like 'M', 'm' and 'O':
![2](/assets/obf_2.jpg)

'M' - means what this stream contains VBA macros with code.  
'm' - means what this stream contains VBA macros without code.  
'O' - means what this is embedded file.  
So, by output from the oledump we can identify lowest one.

>Q3: What is the decryption key of the obfuscated code?

The first thing to do is decompress the macro to see what's in there.  
Select stream with 'M' - in our file its 8, and run this with -v, what mean vba_decompress:
![3](/assets/obf_3.jpg)

After analyzing code, we scroll down and come across an interesting line. Wscript.Run is needed to run another program, which is passed as an argument.  
We look at the first argument and see that the program is called 'maintooljs'. But the 'maintool.js' is run together with some string that is its argument. This argument is our key.
![4](/assets/obf_4.jpg)

>Q4: What is the name of the dropped file?

I have 2 ways to find name of the dropped file. We can run malware on virtual machine or online sandbox (like ANY.RUN) and check it in 'files modification':
![5](/assets/obf_5.jpg)

Or we can turn to the third question, and check what programm is executed by WScript.Run function.

>Q5: This script uses what language?

First - we need to extract this file, manually like python script or with sandbox(check Q4). Second - after reading this code - What do we have in this case? .js extension, Some objects like WScript,  Lets google it.. after typing 'wscript', the first link redirects to learn.microsoft.com, and after we scroll down we see some interesting lines:
![6](/assets/obf_6.jpg)

This is WSH, so lets go google 'what is WSH?'. Go to the relevant wikipedia page, and check first lines. We have two references, so check VBScript and JScript. Analyse it. So we got it.

>Q6: What is the name of the variable that is assigned the command-line arguments?

Start analyse maintool.js. We are lucky and the answer is in the first lines of code, just take a closer look at the WScript.Arguments:
![7](/assets/obf_7.jpg)

>Q7: How many command-line arguments does this script expect?

Another lucky situation. WScript.Arguments return an array of arguments, and these arguments need to be stored in a variable, right? Just take another closer look at first lines, and see how many values are stored from the array.

>Q8: What instruction is executed if this script encounters an error?

For error handling in this case we have try/catch construction. The 'catch' block is executed in case of an error. So take a look inside it.

>Q9: What function returns the next stage of code (i.e. the first round of obfuscated code)?

After a complete code analysis we come across an interesting line:
![8](/assets/obf_8.jpg)

It is too big to be an ordinary text. And also at the very beginning of the code, we have an interesting function called eval() that executes the code from the string.  
So we need a function that will return this giant obfuscated string.

>Q10:The function LXv5 is an important function, what variable is assigned a key string value in determining what this function does?

There is only one string declaration in this function, bruh :D

>Q11: What encoding scheme is this function responsible for decoding?

Let's turn to the answer above, just google the string and you'll get the answer right away

>Q12, Q14, Q15

I want to combine these questions because all the answers will be related to each other.  
Let's figure out what's going on here. We see a function that decrypts something, so in this case I just googled popular ciphers and looked at their algorithms. We see a loop that fills the array with characters from 0 to 256. This also narrows down our search. XOR is also actively used. And this function requires a single key.  
Here's what I found:
![9](/assets/obf_9.jpg)

>Q13: The function CpPT requires two arguments, where does the value of the first argument come from?

Let's see where this function is called:
![10](/assets/obf_7.jpg)

Check the first argument called 'ssWZ'. Remember what it is responsible for?

>Q16: What function is responsible for executing the deobfuscated code?

We have mentioned this function before, just look at the 'try' block at the very beginning and learn more about the inbuilt JScript function that executes code from the string

>Q17: What Windows Script Host program can be used to execute this script in command-line mode?

Let's pay attention to that very wikipedia article about 'WHS'.
![11](/assets/obf_10.jpg)

Based on the description, one of these programmes wscript and cscript is used to run the script. Since I didn't see any window when launching ANY.RUN, therefore it is launched from cmd, not gui.

>Q18: What is the name of the first function defined in the deobfuscated code?

Firstly, we need to decrypt the string where the code is stored with the key (we talked about him in Q3). Just open any convenient website for rc4 decryption, like this:
![12](/assets/obf_11.jpg)

And look at the first line.

> That's all

Thank you for reading guys, thanks to Josh for the challenge, thanks to cyberdefenders community.  
cyberdefenders.org: [click](https://cyberdefenders.org/)
oledump: [click](https://blog.didierstevens.com/programs/oledump-py/)
any.run: [click](https://any.run/)
decode-encode: [click](https://encode-decode.com/)