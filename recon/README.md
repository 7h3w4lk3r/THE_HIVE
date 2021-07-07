---
description: >-
  Reconnaissance is the process of gathering the personal or sensitive
  information about the target by an attacker or pentester.
---

# Recon

The recon phase is the pre-attack phase which the attackers usually spend most of their time on it and almost 80% of time spent on any cyber attack \(even small phishing campaigns or adware attacks\) regardless of the scope, motivation or final goal  is dedicated to the recon phase. the more you know and understand the target system, network, company or person, the better and faster you can find vulnerabilities and weaknesses and when you find your way in, its only a matter of knowing the tools and techniques, bypassing defenses and a little bit of luck.

#### There are 2 types of reconnaissance in cyber security, active and passive. as the name suggests active recon is the process which the attacker actively looks for information and aggressively enumerates the target and might leave some footprints behind. the passive methods are more stealthy and are mostly done using publicly available information and online sources. the best known passive technique is OSINT. 

#### In most training resources you find that the recon process is usually misunderstood or mistaken with its sub-processes like foot printing or enumeration. here i try to explain the interpretation of these words and the differences between them.

## **Information Gathering**

The first sub-process of recon phase which includes finding and locating the target and defining a scope according to the pentest contract or attackers motivations. **information gathering means you are looking for everything you can find about the target regardless of the importance of the information you have gathered.**

as the first step it may include the organization structure, the business partners, workers/employees and staff contact information, possible previous breaches, etc.



## Footprinting

**usually used when gathering info about a single person or a very specific target**.

 for example when we are done with the information gathering process and have found clues about the target we try to focus more on specific details or parts of the organization but the footprinting phrase is usually used for human targets and includes gathering and digging for personal information using social media, social search engines, finding email, home, work addresses, department, employment type, coworkers, family and friends  and even some personal habits that can be useful for us as attackers. 

**most of these information are gathered with help of OSINT sources and techniques.**

## Enumeration

 you have probably heard that "enumeration is the key" mostly in CTF write ups or while taking courses like PWK and this is so true in real world assignments as well. **enumeration means analyzing the information you have gathered for finding the best attack surface or weak spot with the Intel you have on your target.** for example when you run an nmap scan and find a web server the first thing that pops in your head is to check for the web server version, ceck for exploits and go for the web app as well. this is connecting the information you have with the experience/knowledge you have gained with practice.

enumeration should not be mistaken with threat modeling because it threat modeling we are making a structured map of possible holes in the network AFTER we have found these holes using enumeration.

## Scanning

scanning might not always fit in any of the above steps but it is usually done in the enumeration process of recon phase. most used tools are port scanners like nmap and masscan or vulnerability scanners like Nessus or OpenVAS. **scanning is considered the active recon phase when you get in touch with the target and possibly leave some fingerprints behind.**

