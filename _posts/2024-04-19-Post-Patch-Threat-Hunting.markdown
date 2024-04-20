---
layout: post
title: Post Patch Threat Hunting
description: Earlier today MITRE went public stating that a state-backed hacking group breached its systems using two Ivanti VPN zero-days back in January 2024. 
date:   2024-04-19 15:01:35 +0300
image:  '/images/post-patch.jpg'
tags:   [breach, threat hunting, patch, ivanti, mitre]
---
# Intro & Story line
Earlier today MITRE went public stating that a state-backed hacking group breached its systems using two Ivanti VPN zero-days back in January 2024. As you may know by now Ivanti had disclosed 3 zero-days around January this year affecting its Connect Secure and Policy Secure which then prompted the first CISA emergency directive (ED 24-01) of the year. The initial effort was not enough so on February 29, 2024, a joint advisory was released revealing that the integrity checker tool used to detect compromise could be deceived and issuing factory reset was not sufficient to remove the persistence mechanism. 

### MITRE Breach
Fast forward today and the effects of the Ivanti’s zero-days is still haunting us with the new from MITRE. This shows us again that it’s really a matter of when and that it can happen to the best of us. MITRE followed best practices by patching, replacing, and hardening but the threat actor had moved laterally on their network. So, the question that remains is whether they are still on your network.
### Back to the Hunt! Am I Compromised?
At this point we should not merely consider whether we still have the vulnerable appliance or if even your company disposed of it. We are almost 4 months away from when we first heard, and first logs were generated so I hope your data retention policy can help you look that far back if needed be. This is where I would like to talk about or introduce the concept of Post Patch Threat Hunting.

Surely if there is one thing, we should have learned from this Ivanti saga is that patching, factory reset or replacement must not be the end of the hunt. We should ask ourselves what threat actors are exploiting this vulnerability and what would post compromise look like? Essentially, what TTPs are being seen out there and create hunting packages that are easy to manage and upgrade when new intelligence is identified.


### Threat Actor Profiling & Intelligence Gathering
At this point there many available intel, articles written of the activities seen nut perhaps it can be overwhelming to search for them. I am pretty sure many of us already have trusted sources that we go for very detailed analysis but if not and you have to sift through many sources or simply desire to refresh your mind then perhaps this will help. I recently came in contact with OSINTer, “an open-source and extensible platform for collecting and organizing open source intelligence in a way that easily integrates with third party utilities and other pieces of open-source software”. It’s honestly easy to use although I am still learning but can easily put inf front of you many articles to gather intelligence.
![OSINTer]({{site.baseurl}}/images/osinter.jpg)
# Conclusion
Performing Post Patch Threat Hunting is a must and the best practice would be to create hunting query packages that can be easily managed as more intel arises. 

