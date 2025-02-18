---
layout: post
title: Unmasking the Threat∶ A Deep Dive into a Phishing Campaign Targeting Corporate and Educational Emails
description: Massive campaign targets over 27,000+ emails across across 14,000 domains from corporate and education. A threat actor by the name of "Armandabors" on github initially made a commit to github to update the massive list of emails used for one of the phihing domains.
date:   2025-02-17 15:01:35 +0300
image:  '/images/phish.png'
tags:   [Phishing, ThreatIntel, EmailSecurity, StaySafeOnline, CyberAwareness]
---
# Part 1 - The Unmasking 
Massive campaign targets over 27,000+ emails across across 14,000 domains from corporate and education. A threat actor by the name of "Armandabors" on github initially made a commit to github to update the massive list of emails used for one of the phihing domains. 

![commit]({{site.baseurl}}/images/commit.png)

This change in the deployment included few emails of the company I work for which triggered a threat intel alert for unsactioned brand name mentions. The threat actor add a total of 14 repositories with 7 being part of this active campaign and other which seem to have been used for testing. In addition to that,  what appears to be a phishing kit zip folder was still available on one of the repo.

### VIP Email Only Technique
The threat actor/phishing kit employed what I am calling a VIP Email Only technique which the initial page only redirects emails that are part of the allowed email list to the phishing landing page mimicking Microsoft o365. The page simply displays a headline which says "Indentity Verification Required" and asks for the target to enter the user's organization email in order to proceed. This technique seems to be used to provide some sort of legitimacy to the page which also displays a cookie policy banner for the same intent.

![vipcheckin]({{site.baseurl}}/images/vipcheckin.png)

This functionality is found in the javascript file named "validate-email.js" loads the list of allowe emails from the "allowedEmails.json" file into cache and if the email matches the ones on the list then it will redirect to the phishing page encoding the email into base64 and concatenates it to the base url as seen in the code snippet below.

![snippet1ifallowed]({{site.baseurl}}/images/snippet1ifallowed.png)

One domain is being used for multiple repositories targeting different users but they all use the same phishing kit. It uses Vercel for automatic deployment of the initial page which verifies the identity of the user before redirecting the allowed user to the phish landing page.

![phishpage]({{site.baseurl}}/images/phishpage.png)

This was true of almost every active campaign available ont the threat actor's repo excpet for one, the repository "servdatapage", which had a totally different look and technique.

![servdatapage]({{site.baseurl}}/images/servdatapage.png)

### The Odd One
This repositor was much simpler in it's deployment and looks. The threat actor was still using Vercel for deployment but in this one there was no other trick to avoid analysis. Written in HTML and PHP, this was a simple phishing page which that would send the information enter by the user to a telegram channel.

Surely if there is one thing, we should have learned from this Ivanti saga is that patching, factory reset or replacement must not be the end of the hunt. We should ask ourselves what threat actors are exploiting this vulnerability and what would post compromise look like? Essentially, what TTPs are being seen out there and create hunting packages that are easy to manage and upgrade when new intelligence is identified. 

Before we go into the details of the telegram channel and bot, a log file which perhaps was commited by mistake reveals what type of data was being collected withough having to look into the PHP code. The information collected were username and password, client IP, and user agent. We also find perhaps one new person of interest by the tag "Abilitytiger".

![logs1ns]({{site.baseurl}}/images/logs1ns.png)

I am currently obfuscating this portion because although the data seems to indicate that it was part of a test run perhaps on the threat actor's environment, which would be interesting, yet I can't be sure. I am in the process of reporting all the infrastructure I found so far and I am in possession of the phishing kit as well as the list of all emails which I can provide with discretion. This can be requested on my LinkedIn or X handle.

On Part 2 I will focus more on the telegram channel and bot OSINT and my attempt at attribution. Now go eat your IOCs.

## IOCs
* docuaccount[.]com  
* servdatapage[.]vercel[.]app  
* folder[.]docuaccount[.]com  
* newcompanypolicy[.]vercel[.]app  
* 08fold[.]docuaccount[.]com  
* rankine[.]vercel[.]app  
* rankine[.]docuaccount[.]com  
* hp-f-com-au[.]vercel[.]app  
* sf[.]echospheure[.]ru  
* mycpa-silk[.]vercel[.]app  
* mycpa[.]docuaccount[.]com  
* ams-pink[.]vercel[.]app  
* andexy[.]docuaccount[.]com  
* littlelionentertainment[.]docuaccount[.]com  

### Stats
Here are the top 10 email domains and the number of emails they contain:

* pcsonet.com – 1,500 emails
* tvb.com.hk – 400 emails
* pwc.com – 154 emails
* sbtaxcpa.com – 127 emails
* claconnect.com – 97 emails
* ey.com – 86 emails
* winnco.com – 70 emails
* worldclimatefoundation.org – 59 emails
* brazosport.edu – 58 emails
* trocaire.edu – 55 emails 