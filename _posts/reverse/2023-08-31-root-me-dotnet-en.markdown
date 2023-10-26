---
layout: post
title:  "EN | Reversing 'PE DotNet - 0 protection' from root-me.org"
date:   2023-08-31 18:29:02 +0300
categories: reverse
---
![First](/assets/ch22.jpg)  

Lets take an easy, but interesting task of reverse C# application. 

At first step we are going to check it is really .NET application and use 'file' utility (I'll do it through MINGW64):
![Second](/assets/file_ch22.jpg)

Running the ch22.exe file, we will see this:
![Third](/assets/ch22_on.jpg) 

If we are simply upload our binary into Ghidra, - we will see unidentified bytes. It is happens because .NET compiles into IL bytecod, not executable. Ghidra dont support this architecture, so, after googling we can find a lot of tools to reverse DotNet applications.
I'm choosed the [dnSpy](https://github.com/dnSpy/dnSpy). (You can read about this tool on GitHub page.)
Download, Setup, Open GUI version:
![Four](/assets/dnspy_ch22_1.jpg)

Next step,  
File -> Open... / CTRL + O  
And open our .exe  
Now we are interested in the column "Assembly Explorer". We run our eyes all over the catalog, intuitively discard unnecessary tabs, and finally find the Form1 class.  
We look at the code inside the class and realize that this is exactly what we need.  
Scrolling through the instructions, we come across the Button1_Click function and a suspicious CompareStrings inside it:
![Five](/assets/dnspy_form1.jpg)


We look at the arguments, find the line "DotNetOP" and try to enter it as a password:
![Six](/assets/ch22_suc.jpg)

Great Job, + 10 points.