# THM [TryHackMe] Hollow Shell (Medium) (Web Exploitation)
![HollowShell](/Images/HollowShell%20CTF/HollowShell_1.png)

## Description
![HollowShell](/Images/HollowShell%20CTF/HollowShell_2.png)

### 🛎️ Concierge Briefing
You find it on the beach: pretty, ordinary, the kind of thing nobody thinks to check. Slip something inside and hold it to your ear.

The Byte Lotus beachfront lets guests personalise their in-room display by uploading a shell — a little souvenir pack of shoreline ambiance. Staff publish them through the Shoreline Display portal, and once a shell is "held to the room's ear" it plays its shore. Slip past what the portal forgets to check, and the shell answers with a shell of your own.

## Write-Up

### 1. Reading and Understanding the CTF

Judging by the description it seems the challenge is pointing towards opening a reverse shell with this line "the shell answers with a shell of your own." so our goal is to first explore the IP given and figure out to open a reverse shell.

With most of the THM challenges involving SSH/Web stuff I start with doing a nmap scan using "-sC -SV -oN" which is my go to command line when doing challenges in THM to quickly see what services are running on the IP.

A little more context on what the options are.

-sC is basically a default scan to find and check if the web ports commonly (443/80), SMB Ports Commonly (445) and SSH Ports (22).

-sV Communicates with the port to see what software and verson is running on it to send back info to the user.

-oN Just for me to put the output into a txt file for reference.

![HollowShell](/Images/HollowShell%20CTF/HollowShell_3.png)

From the output I can see that the IP has a SSH on port 20 and a http service on port 5000. With this its clear that a reverse shell must be made.

### 2. Scouting the http

Using the info from the nmap scan I searched up the http using the browser with the given IP and Port simply putting "CTF IP:5000" Brings me to a website with a login portal.

![HollowShell](/Images/HollowShell%20CTF/HollowShell_4.png)

First thing I tried is to do the classic admin/admin user and password. But it did not yield any result, next I went to check the page source code and in the comments there was a note from the developers giving me a username and a password for a account.

![HollowShell](/Images/HollowShell%20CTF/HollowShell_5.png)

Using the given credentials I was able to get into a file submission page, more specifically a zip file uploader. In the description there are a number of files which are vaild to be used in the zip file and the zip file must contain the file "shell.json"

![HollowShell](/Images/HollowShell%20CTF/HollowShell_6.png)

My first thought just to test the limits of the file upload system to see if i can try sending anything other than a zip file or a zip file with no shell.json which yielded nothing. 

![HollowShell](/Images/HollowShell%20CTF/HollowShell_7.png)

![HollowShell](/Images/HollowShell%20CTF/HollowShell_8.png)

After messing around with the shell.json code I was able to get the supposed json contents it which are it has to have a "name:" variable and "assets:" list where I can name other files within the zipfile. So I ended up with

```
{
    "name":
    "assets":[

    ]
}
```

![HollowShell](/Images/HollowShell%20CTF/HollowShell_9.png)

### 3.Finding the exploit/vunrability

With this info I tried doing a Json Injection by putting some bash commands within the shell json in hopes of opening a reverse shell. But it did not work it as It would either just print the output on the name or within the given file directory.

Without any options left within my knowledge I decided to do some reseach on some exploits related to .json files and python script execution related to .json files. 



