---
title: "Pentest Note of the Day: SecLists – A Core Resource for Offensive Security Testing"
description: An overview of the SecLists repository, its contents, and usage examples for penetration testing.
author: hx77
date: 2025-08-17 22:00:00 -0400
categories: [note_of_the_day]
tags: [note_of_the_day ]     # TAG names should always be lowercase
pin: false
image: 
    path: https://imagedelivery.net/-rYpVInpJDYYl5Fx06AU4g/cafdccc5-c452-4fc9-bada-4d7f479d6500/public
alt: Pentest Note of the Day: SecLists
---

SecLists is an open-source collection of multiple types of lists commonly used during security assessments. It is maintained on GitHub by Daniel Miessler and contributors, and has become a standard reference for penetration testers, red teamers, and security researchers.  

------
## Contents of SecLists

### 1. Passwords
This section includes the well-known `rockyou.txt` file, as well as curated top 10k/100k password sets. These are used for online password spraying, brute forcing, and offline hash cracking.

![Passwords folder screenshot](/assets/img/seclists/passwords.png)
_Placeholder for screenshot_

**Example (Hashcat):**
```bash
hashcat -m 1000 hash.txt /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt
