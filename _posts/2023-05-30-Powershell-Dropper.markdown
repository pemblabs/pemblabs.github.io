---
layout: post
title:  Powershell Dropper
description: I found an interesting PowerShell script uploaded today on Malware Bazaar uploaded at 2023-05-31 02:06 (UTC) then turns out to be a dropper...
date:   2023-05-30 15:01:35 +0300
image:  '/images/20.jpg'
tags:   [malware, dropper]
---
I found an interesting PowerShell script uploaded today on Malware Bazaar uploaded at 2023-05-31 02:06 (UTC) then turns out to be a dropper.
Malwarte Bazaar is an online platform and repository used by many analysts and reverse engineers looking to collect and share information about malware samples and artifacts.

This particular sample was a malicious PowerShell script dropper file to deliver and install additional malware. A delivery mechanism that is very much known in the community.
Upon analyzing the file first thing you see is that it's ASCII encoded in order to obfuscate its purpose.

![ASCII Encoded]({{site.baseurl}}/images/20-1.jpg)
*ASCII encoded PS*

So continuing with static analysis of the file I decided to write two simple Python scripts that should do the job to deobfuscate this file. First I copied the obfuscated strings into a file and then create a simple carver to get rid of the unnecessary strings.

``` Python
with open('psscript.txt','r') as f:
        ara = f.read().replace("[char]", "")
        print(ara)
        ara_ara = ara.replace("+", ",")
        print(ara_ara)

```

After that, all that was left now is to decode the ASCII strings. For that Python has a built function to make the job easier. I first put each individual carved string into 4 different variables and loop through and joined them to get the intended result.

```Python 
decoded = ''.join(chr(i) for i in snip1)
print(decoded)
decoded = ''.join(chr(i) for i in snip2)
print(decoded)
decoded = ''.join(chr(i) for i in snip3)
print(decoded)
decoded = ''.join(chr(i) for i in snip4)
print(decoded)

```

And this is the result of the deobfuscated strings ( don't mind the printed statements it's good to know how the code is doing 😁). So you can see where this is going.

![Decoded PS script]({{site.baseurl}}/images/20-2.jpg)
*Decoded PS script*

So now let's put our decoded strings into their respective place and take a bird's eye view to see how the script actually is and go over what it does.

![Encoded and Decoded script]({{site.baseurl}}/images/20-3.jpg)
*Encoded and Decoded script*

Now that we have the beautiful thing here, what is it actually doing?
First, it creates an alias called "rzs" which is used to execute a series of commands using the "invoke-expression". It assigns a value to the "global:block" variable to create a new instance of "System.Net.WebClient" class and then invokes the method "DownlaodString" of the "WebClient" instance with our precious URL "hxxp://dz7eg[.]top/515/sdfzw.php?i=".

One interesting is that it concatenates the hostname of the machine to the URL as a parameter perhaps to keep track of the infected devices.
If it survives the week perhaps I will see if we can get that 2nd stage payload.
Hope you enjoyed it 🙂 God bless you!

## IOC
* SHA256 hash: 9d1582c69b56d7279f4962a9648524d9c57613b8fc2f7ad74fc2fd55f748e200
* SHA1 hash: c0b8449c705d2603a419f210ad3c7a2f98941b9b
* MD5 hash: c027bbcb1ccdc36e4b742b8d445b73fa
* URL: hxxp://dz7eg[.]top/515/sdfzw.php?i=
