---
layout: post
title:  "EN | 'XLM Macros by Josh Stroschein' from cyberdefenders.org"
date:   2024-01-02 09:00:02 +0300
categories: forensic, reverse
---
![0](/assets/XLM_0.jpg)

>Q1: Sample1: What is the document decryption password?

First, we need google some tools, and Didier Stevens comes to the rescue again. He has a lot of useful tools, one of which is 'msoffcrypto-crack.py'. [You can familiarise yourself with the tool by clicking here.](https://www.youtube.com/watch?v=4DL9U_DfC0w/)  
So, after install, type "msoffcrypto-crack.py filename":  
![1](/assets/XLM_1.jpg)

>Q2: Sample1: This document contains six hidden sheets. What are their names? Provide the value of the one starting with S.

First, we need to decrypt our file with password from Q1. In this case we're going to use ["msoffcrypto-tool."](https://github.com/nolze/msoffcrypto-tool/) Just type:  
"msoffcrypto-tool input_file output_file -p password"  
and go to the next step.  
![2](/assets/XLM_2.jpg)

Next, we need to deobfuscate this docx file with ["XLMMacroDeobfuscator".](https://github.com/DissectMalware/XLMMacroDeobfuscator/) Type:  
"xlmdeobfuscator --file filename"  
then check and analyse the output.  
![3](/assets/XLM_3.jpg)

There you go.

>Q3: Sample1: What URL is the malware using to download the next stage? Only include the second-level and top-level domain. For example, xyz.com.

Based on the previous answer, we can check the ouput again, and find WinApi function called "URLDownloadToFileA".  
Just check the arguments.

>Q4: Sample1: What malware family was this document attempting to drop?

We need to run our sample, check the modified(created) files and upload dropped file on virustotal.  
But because I have a very slow VM, i will simply upload our sample1.bin on VirusTotal.  
Go to Details->Names, and we notice a very interesting name associated with malware family:  
![4](/assets/XLM_4.jpg)

We're done with the first sample, let's move on to the second.

>Q5: Sample2: This document has a very hidden sheet. What is the name of this sheet?

Lets try again use XLMMacroDeobfuscator to find hidden sheet:  
![5](/assets/XLM_5.jpg)

Check the beggining of the output.

>Q6: Sample2: This document uses reg.exe. What registry key is it checking?

In the details of the task on the website, we can find an interesting plugin - "PLUGIN_BIFF". Lets use it:  
![6](/assets/XLM_6.jpg)

After looking through all the lines (boring) at the very bottom we come across what we are looking for.  
First, the code opens a certain registry section, then saves it to file 1.reg, goes to sleep for a while and opens the saved file.  
Next, we encounter the FREAD function and the line below it. This line is the key.  
![7](/assets/XLM_7.jpg)

>Q7: Sample2: From the use of reg.exe, what value of the assessed key indicates a sandbox environment?

In this question I will use XLMDeobfuscator because the deobfuscated code will be more understandable. We need to analyse operations with the registry again:  
![8](/assets/XLM_8.jpg)

In the screenshot you can see a conditional expression that looks for a specific value from the register. (I just compared the code from oledump and xlmdeobfuscator) If it is found, it closes the worksheet. So this value would be the answer to the question.

>Q8: Sample2: This document performs several additional anti-analysis checks. What Excel 4 macro function does it use?

Looking a little further down, we find more conditional constructs where close() is called:  
![9](/assets/XLM_9.jpg)

We can also see the function that is used inside the construct. We need this one.

>Q9: Sample2: This document checks for the name of the environment in which Excel is running. What value is it using to compare?

We look further down and we find this line:  
![10](/assets/XLM_10.jpg)

Check the line inside the function SEARCH().

>Q10 and Q13:

Move through the code, until we get to the load and execute functions. This is where we find ourselves:  
![11](/assets/XLM_11.jpg)

After the file download function, we see the .exe program call in CELL J9. Everything is clear from the name.

>Q11: Sample2: What URL does the malware download the payload from?

Just check the passed arguments in URLDownloadToFileA function in the previous screenshot.

>Q12: Sample2: What is the filename that the payload is saved as?

Based on the previous answer, check the next argument

>Q14: Sample2: What was the malware family?

Upload our sample2 on VirusTotal or google the site that the programme uses.

> That's all

Thank you for reading guys, thanks to Josh for the challenge, thanks to cyberdefenders community.  
cyberdefenders.org: [click](https://cyberdefenders.org/)  
