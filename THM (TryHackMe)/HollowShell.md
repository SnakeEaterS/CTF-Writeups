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

During my research I came accross a vunrability which its use case was similar to the CTF which is called 'ZipSlip' essentially what this does it allows someone to write or overwrite files outside the target leading to a remote command execution upon extraction which is perfect for this CTF.

### How a 'Zip Slip' Works 

- Uses an archive where file names directory traversal methods like ../../

- When the app extracts the zip file it combines the 'directory path' file name from the archive without checking if the result escapes the target folder.

- with the file name being a file traversal methods the system reads it like instructions and puts the file on the said file path.

With this it gave me the idea of using this vunrability to put files where I want in the system. When reading the description of the page it says "A shell may include optional automation hooks" this tells me there is a folder named "hooks" within the system that has automation/cron job running in there executing files.

![HollowShell](/Images/HollowShell%20CTF/HollowShell_10.2.png)

First I made a python script that reads and zips files in my case the shell.json file and the python script which I will zip slip to the hooks folder by using the directory naming method '../'. 

Why did I make a custom zip script? To put it simply when I tried zipping it normally with the zip command it just auto changed the name of the file to 'hooks/pickup.py' this is probably a security feature of the zip command so I made this instead.

![HollowShell](/Images/HollowShell%20CTF/HollowShell_10.1.png)

Next was to make the actual script to be executed, since our goal was to open a reverse SSH shell this script exploits the automation in the hooks folder to basically tell the computer to create a network socket with the IPv4/TCP connection and hand me a shell by connecting to my IP and port 1234. 

Next it streams redirection using the loop. as linux every open channel for reading and writing has a file descriptor. By default it starts with 3 channels 

- 0 standard input/stdin which gets inputs
- 1 standard output/stdout which displays text output on the screen
- 2 standard error/stderr displays error

So this loops helps to find the internal file descriptor number to allow the reverse shell to display on the attacker screen in my case my linux terminal.

Then the last line is just to spawn the reverse shell by calling /bin/bash

### 4. Setting up the exploit and exploring the shell

Now all I need is to listen for the script when the automation runs it when I upload the zip file. To do this I set up a net cat to listen for it my go to command line for nc -lvnp. A quick and simple listener to listen, notify, look at numeric IPs and the specific port.

![HollowShell](/Images/HollowShell%20CTF/HollowShell_12.png)

Next is to zip the payload script, check the contents and send it over to the website.

![HollowShell](/Images/HollowShell%20CTF/HollowShell_13.png)

Once sent I waited for a little since cron jobs repeat in intervals so I came back after a minute to check my listener and I was able to get a reverse shell now knowing THM their flags are usually hidden in the homes folder so I beelined towards the home directory and found a folder named 'roomservice' where the flag text file was and got the flag!

![HollowShell](/Images/HollowShell%20CTF/HollowShell_14.png)

![HollowShell](/Images/HollowShell%20CTF/HollowShell_final.png)

## Flag 🚩
<details>
    <summary>Flag</summary>

    THM{z1p_sl1pp3d_1nt0_a_sh3ll}

</details>

## What I learned 
This CTF showed me that a small mistake like leaving a comment in the system can lead to an attacker having access to a system. Overall was a pretty fun experience would do it again.

Knowledge Gained:
- File Descriptors
- Zip Slipping exploitation
- Creating a reverse shell using a python script
- Web Enumeration




<sub>Done on 17/9/26</sub>