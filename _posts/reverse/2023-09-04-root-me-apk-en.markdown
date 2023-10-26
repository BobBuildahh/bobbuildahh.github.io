---
layout: post
title:  "EN | 'APK - Introduction' from root-me.org"
date:   2023-09-03 18:29:02 +0300
categories: reverse
---
![First](/assets/bobo.jpg)
Today our test subject is an android app, so we need to stock up on the necessary tools. You can find them yourself, or you can use my favorites:

[jadx](https://github.com/skylot/jadx)  
[adb](https://developer.android.com/tools/adb)  
[smali debugger](https://github.com/skylot/jadx/wiki/Smali-debugger)

Since this is an easy task, we can dispense with the debugger and dynamic analysis by using only the jadx.  
Open basic_rev.apk on your phone or in an emulator and try to enter a random password to find out more information about the strings that are present in the file.
![Second](/assets/apk_fail.jpg)

Open jadx-gui, upload the .apk and for now we are interested in the code directory. (left widget)  
Go to  
basic_rev.apk -> Code -> com -> example.basic_rev  
And here we are met with 3 classes - BuildConfig, MainActivity, R. But based on the title, we're only interested in the second one.  
We open and scroll a little bit, we can notice the very line "Try again ;)" in the if-else block. Looking at the condition, we see that our entered string is compared to the variable seed, which must be converted somehow in the makeFlag() function:
![Third](/assets/apk_main.jpg)

Let's figure out what the seed variable is and then look at the function.  
Resources -> resoursec.arsc -> res -> values -> strings.xml  
After looking through the whole strings file, we come across this line at the end:
![Four](/assets/apk_var.jpg)

Back to MainActivity class, and we see that the seed variable must pass through makeFlag() function, before our strings can be compared.  
Let's take the easy way, open a browser, write Online Java Debugger and just copy the whole function by substituting the value of the seed variable into the argument:
![Five](/assets/java_debug.jpg)

Get the string "@ndro01d", try it as our password:
![Six](/assets/apk_suc.jpg)
