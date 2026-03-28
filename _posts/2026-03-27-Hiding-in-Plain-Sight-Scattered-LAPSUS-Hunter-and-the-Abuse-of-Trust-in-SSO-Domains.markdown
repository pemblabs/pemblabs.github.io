---
layout: post
title: "Hiding in Plain Sight∶ Scattered LAPSUS$ Hunter and the Abuse of Trust in SSO Domains"
description: How a single apex domain, dozens of brand-themed hostnames, smishing, and public Telegram recruitment fit together in one identity-focused pipeline, and what still belongs in the “open questions” column.
date: 2026-03-27 12:00:00 +0000
image: '/images/slh-passkeysso-subdomains.png'
tags: [Phishing, ThreatIntel, smishing, vishing, telegram, identity, SSO, MITRE]
---

Passkeys were sold to users as a way to shrink the phishing surface. Attackers responded the way they always do: they kept the *story* (“set up your passkey”) and swapped the trust anchor for a hostname that *sounds* like the thing you already believe in. When that story rides on SMS and on a single domain that **accumulates a long tail of subdomains**, the pattern usually means **scale**, not a one-off lure.

This post ties together three artifacts I collected while working the Scattered LAPSUS$ Hunter (SLH) angle: a **smishing** message pointing victims at a fake “passkey setup” portal, a **subdomain inventory** that shows how one apex domain can impersonate many brands at once, and a **Telegram recruitment post** from **Scattered LAPSUS$ Hunter’s Telegram presence** that advertises paid helpdesk calling work. I read these as parts of the **same operational picture**: commodity identity abuse fronted by SSO-themed naming, with a labor market on the side that buys scripted voice calls against helpdesks.

## The lure: “your passkey portal” in SMS

The smishing screenshot is blunt. The sender tells the recipient that their “passkey setup portal” lives at an `hxxps://` URL under **`passkeysso[.]com`**. The handset UI flags the thread as an unknown sender and nudges the user toward reporting spam, which is correct, but it is also a reminder that **carrier filtering and user prompts arrive after the message already landed**.

What matters analytically is the **narrative fit**. Passkey rollouts are noisy; helpdesks are busy; users are primed to complete a “setup” step. A domain that **puts *SSO* and *passkey* in the same registrable name** is engineered to ride that confusion. I am not showing the full hostname here beyond the apex; the IOC section lists representative samples in defanged form.

![Smishing lure pointing to passkey-themed host under passkeysso[.]com]({{site.baseurl}}/images/slh-passkeysso-smishing.png)
*Smishing message referencing a fake passkey setup URL (partial hostname redacted in source image).*

## One apex, many faces

The subdomain view I captured is the clearest sign of **automation or templated provisioning**. Under **`passkeysso[.]com`** I see a long tail of hostnames: some look like **brand- or sector-themed labels** (including names you would associate with energy and other industries), others look like **noise, tests, or in-jokes**, including strings that taunt researchers. Many rows carry **non-zero detection ratios** in **VirusTotal**’s subdomain listing (and related community scoring), and the records cluster on **shared anycast-style resolution** patterns you expect when the front end sits behind a CDN.

I am deliberately not turning the body of the post into a name-by-name parade. The point is structural: **one registrable apex**, **many virtual hosts**, **shared infrastructure**, **mixed serious and throwaway labels**. That combination is what I expect when someone is rotating brands to find clicks, not when they are running a single bespoke phish against one employer.

![Subdomain listing for passkeysso[.]com]({{site.baseurl}}/images/slh-passkeysso-subdomains.png)
*Subdomain inventory illustrating scale and mixed naming under a single apex.*

**As of my review (March 2026),** **`passkeysso[.]com`** was already attracting **malicious classifications** from multiple engines on VirusTotal. Separately, the **TLS certificates** I saw were still **within their validity window** and **not shown as revoked** in the tooling I used, meaning there was **no obvious takedown** from that angle then. Status changes quickly; re-check VT and certificate transparency logs before you rely on this in an incident ticket.

## The other half of the pipeline: buying voices for helpdesks

The Telegram screenshot comes from **Scattered LAPSUS$ Hunter’s** Telegram presence: the channel brands itself as part of a **global cybercrime “backbone”**, and the post recruits callers for **paid helpdesk work**. The operator asks for a **voice message** to start, promises **scripts**, and quotes **per-call payouts** tied to success. Recruits are told to contact **`@SLSHSupport`**. The same **SLH** handle naming appears on that recruitment surface.

![Telegram recruitment post for scripted helpdesk calling]({{site.baseurl}}/images/slh-voice-cloning-recruit.png)
*Public recruitment post: voice sample requested, scripted helpdesk calls, per-call compensation.*

I treat this as **strong, public evidence of vishing supply**: someone is commoditizing **human voice** and **helpdesk social engineering** as a service. What the screenshot **does not prove** by itself is **AI voice cloning**. Asking for a voice note is also consistent with **vetting**, **quality control**, or **later replay**. I leave **cloning** in the **open-questions** bucket until there is technical evidence (artifacts, tooling, or confirmed modus operandi beyond the post).

## Why SSO-themed naming hits harder than a generic phish

Enterprise identity is a chain of trust: **IdP**, **SSO**, **MFA**, **device posture**, **helpdesk recovery**. Attackers only need to **weaken one link**. A domain that *sounds* like “passkey + SSO” is aimed at the **mental model** users already have, not at the cryptographic details they do not see.

For defenders, the actionable threads are:

- **Brand and hostname monitoring** for new registrations and wildcard children that track your industry peers, not only your own trademarks.
- **SMS and messaging abuse** reporting loops; treat **passkey-themed** templates as first-class in SOC playbooks.
- **Helpdesk controls** that resist “I just need a reset” velocity: callbacks, manager escalation, and device-bound recovery.
- **Voice-in-the-loop verification**: treating **voice-only** trust as both a **policy** and a **tooling** problem, especially when adversaries buy scripted calling labor at scale.

## IOCs (defanged, representative)

Apex and a **non-exhaustive** sample of hostnames observed in my materials (defanged). Add your own full exports in your threat platform if you need complete coverage.

| Type | Value |
|------|--------|
| Apex domain | `passkeysso[.]com` |
| Representative hostnames | `www[.]passkeysso[.]com`, `shiny[.]passkeysso[.]com`, `ihateshinyhunters[.]passkeysso[.]com`, `fakesubdomainthisisnotreal[.]passkeysso[.]com`, `random[.]passkeysso[.]com`, `ovintiv[.]passkeysso[.]com` *(example of brand-themed label)* |

## MITRE ATT&CK (Enterprise)

Mappings below are **analytic labels** for how I read this activity, not a claim that every sub-technique was observed end-to-end in one intrusion chain. Technique names and IDs follow [MITRE ATT&CK](https://attack.mitre.org/) (Enterprise).

| ID | Technique | Relevance here |
|----|-----------|----------------|
| [T1566](https://attack.mitre.org/techniques/T1566/) | Phishing | Umbrella for delivery of malicious links and voice-centric lures. |
| [T1566.002](https://attack.mitre.org/techniques/T1566/002/) | Spearphishing Link | SMS smishing with a URL to a **fake “passkey setup”** host under **`passkeysso[.]com`**. |
| [T1566.004](https://attack.mitre.org/techniques/T1566/004/) | Spearphishing Voice | Recruitment and scripting for **helpdesk-oriented voice** work (vishing supply), as shown in the Telegram artifact. |
| [T1583.001](https://attack.mitre.org/techniques/T1583/001/) | Acquire Infrastructure: Domains | Use of a **registrable apex** and **many subdomains** to stage brand-themed lures and pass traffic through shared front-end infrastructure. |

---

*Footnote: This write-up is my read of public artifacts and tooling output I captured at the time of research. It is not legal attribution, HR or employer intelligence, or a statement on behalf of any vendor. Re-verify IOCs, certificates, and VT scores before operational use.*
