---
layout: post
title: Unmasking the Threat∶ Telegram OSINT and Attempt at Attribution
description: Last time on part 1 we made a deep dive into the overal campaign infrastructure operation revealing multiple indicators and targeted groups. This time Ia am going to focus on the findings related to the odd one, referering to the unique phishing page, I uncovered during the investigation... 
date:   2025-03-01 15:01:35 +0300
image:  '/images/telegramosint.png'
tags:   [Phishing, ThreatIntel, telegram, osint, bot, attribution]
---
# Part 2 - The Telegram OSINT
In part 1, we took a deep dive into the overall campaign infrastructure operation, revealing multiple indicators and targeted groups. This time, I am going to focus on the findings related to the odd one, referring to the unique phishing page I uncovered during the investigation. 

![pipeline]({{site.baseurl}}/images/pipeline.gif)
*Credential Theft Pipeline*

Among all the repositories with the same phishing kit deployment, the repo "servdatapage" had a different phishing kit that did not employ the "VIP Email Only" technique. The kit which seems to have been created by another threat actor by the name of "Abilitytiger" had a simple look mimicking a webmail. The webpage included an event listener function for the submit button that captures the email and password and sends data via AJAX POST request to the "get1.php" file. The "get1.php" is responsible for collecting additional host information, constructing the message body with the collected host data before sending it to an email, telegram channel, and saving it locally.

![dataprep]({{site.baseurl}}/images/dataprep.png)
*Message Construct*

The credential is distributed in a way that would provide redundancy and resiliency by saving it in 3 completely different ways.
First, it was sent to the "norereply@noway.com" email. A quick OSINT at the domain, reveals other emails under the "noway.com" domain such as "fake@noway.com", "nevermind@noway.com" and similar.

![noreply]({{site.baseurl}}/images/noreply.png)
*Send-to email*

Second, we see the file "log1ns.txt" being opened to append the harvested credentials and host data locally. This file is the same which was most likely committed to GitHub by mistake and revealed potentially the location of our threat actor.

![locallog1ns]({{site.baseurl}}/images/locallog1ns.png)
*Credentials Saved Locally*

Lastly, the same message constructed with the harvested credentials and host data is sent to a telegram bot chat ID. Having the chat ID and token available made it possible to do telegram OSINT and find additional information on our threat actor and its extended infrastructure. 

![servdatapage]({{site.baseurl}}/images/telgrambot.png)
*Telagram Token and ChatID*

### Telegram OSINT 101
In order to safely do OSINT and gather additional information, I decided to use the Tor browser to interact with the Telagram's API through GET requests and commands. I initially constructed the request with the "getUpdate" command but we got an error as a response, so I used the "getMe" and gave the formatted response below.

```json
{
  "ok": true,
  "result": {
    "id": 7192456887,
    "is_bot": true,
    "first_name": "PrettyDay2024",
    "username": "RageDaybot",
    "can_join_groups": true,
    "can_read_all_group_messages": false,
    "supports_inline_queries": false,
    "can_connect_to_business": false,
    "has_main_web_app": false
  }
}
```
The construct is as simple as sending a Get request to "hxxps://api[.]telegram.org/bot'<'YourBotToken>/'<'command>". This request revealed the first name under the account but most importantly that it is indeed a bot as we see the value "is_bot" returned "true" and the name of the bot.

![ragedaybot]({{site.baseurl}}/images/ragedaybot.png)
*Telagram bot RageDaybot aka PrettyDay2024*

Navigating to the profile page on the Telegram app didn't reveal any new information. Not having much to go about from there all that was left was our chatid. Having run multiple commands, the most useful was the "getChat" which provided us with new findings.

The chat ID does not belong to a group or channel since it was a positive number instead of a negative number. You might think this could be inferred from the chatis which I already had, but it is not so since the API query would have not contained a hyphen. The response also revealed an interesting name for the account as shown below.

```json
{
  "ok": true,
  "result": {
    "id": 1713866119,
    "first_name": "Local",
    "last_name": "PD",
    "username": "DogeatDogWorldx1",
    "type": "private",
    "can_send_gift": true,
    "active_usernames": [
      "DogeatDogWorldx1"
    ],
    "photo": {
      "small_file_id": "AQADAQADdrExG5aYSUUACAIAA4eFJ2YABES8BJOCNwVJNgQ",
      "small_file_unique_id": "AQADdrExG5aYSUUAAQ",
      "big_file_id": "AQADAQADdrExG5aYSUUACAMAA4eFJ2YABES8BJOCNwVJNgQ",
      "big_file_unique_id": "AQADdrExG5aYSUUB"
    },
    "max_reaction_count": 11,
    "accent_color_id": 0
  }
}
```
### Put a Tin Foil Hat On

If you are following the story you would remember that in Part 1 I revealed that the most targeted organization after aggregating all the emails I found on the threat actor's GitHub repositories, was the "pcsonet.com" with exactly 1,500 emails targeted. Well if you go to "pcsonet.com" you will be redirected to "pinellassheriff.gov" which is the Pinellas County Sheriff's Office. You might also wonder whether this is an insider threat issue or a disgruntled cop. Who knows?

![pcsonet]({{site.baseurl}}/images/pcsonet.png)
*Pinellas County Sheriff's Office*

As you can see the API response reveals the first_name to be "Local" and the last_name "PD". Whether this is a coincidence I will leave that up to you, but let's follow the trail and see where I ended. In addition to that, we see an interesting username "DogeatDogWorldx1" and we see a profile photo on the account.

![dogeatdog]({{site.baseurl}}/images/dogeatdog.png)
*Local PD aka DogeatDogWorldx1*

Still, with the tin foil hat, I found that "DogeatDogWorld" seems to be a thing or perhaps it's a unique trait of our threat actor. Multiple accounts with similar usernames were recently active with others being inactive for a while. The Local PD was the most recently active and I witnessed as well showing "active 5 minutes ago".

![dogeatdog profiles]({{site.baseurl}}/images/dogeatdogprofiles.png)
*DogeatDog Profiles on Telegram*

### Attribution - The A Word

Before I go on to present additional findings and even point out similarities to other independent accounts or users, I must state that this by no means should be used to derive a conclusion on any participation or authorship in this activity by the said accounts. Having thought much about it as well, I deemed it only fair to present all findings and the connections made from all the dots.

### Who Are They?

So I continued gathering open source intelligence on our Local PD in the same manner I was conducting. I retrieved all the forms of profile photos which basically only difference in size most likely for different devices. My attempt was to see if there were any other past profile photos.

![pd photos]({{site.baseurl}}/images/pdphotos.png)
*GetUserprofilePhotos Response*

Using the image I was able to retrieve, I performed a reverse image lookup which took me to a "fandom.com" of VRChat Legends displaying similar and the exact same banner found in the telegram profile photo.

![fandom]({{site.baseurl}}/images/fandom.png)
*VRChat Legends Wiki*

Having navigated around, I found that the page has been actively maintained over the years and that the purpose of the community is to join a Virtual Reality chat world. Their characters mostly resemble a cat which makes sense because one of the banners says "Neko Police", neko being the Japanese word for "cat". All of this information about the nature of the fandom community I found from videos and images they post on their discord server. The discord server has a lot of members and many of them are very active with about 6k+ online at once.

![localpd discord]({{site.baseurl}}/images/localpddiscord.png)
*VRChat Legends Fandom Discord*

### Additional Cross Reference and Conclusion

Well as I feared, the article has become too long for my liking so here are some additional findings and conclusions for this.

I did cross reference the usernames I found so far to see any commonality and this was true for "Abilitytiger", the phish kit maker, and "RageDaybot".  

![abilitytiger reference]({{site.baseurl}}/images/abilitytiger.png)
*Abilitytiger username Refences*

![ragedaybot reference]({{site.baseurl}}/images/pronounybot.png)
*Ragedaybot username Refences*

Although many of the references were inactive, both usernames were found to have references to a site called "Pronouny" which I found its repo on GitHub. The repo is maintained mainly by two users who appear to be seasoned developers. 

![pronouny]({{site.baseurl}}/images/pronouny.png)
*Pronouny Git Repo*

In short, I can finally confirm that the threat actor Armandabors' GitHub account has been disabled and hopefully disrupt the operations. 

Happy Hunting!