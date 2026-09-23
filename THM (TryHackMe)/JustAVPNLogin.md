## TryHackMe Just A VPN Login (Easy) (Forensics)

![VPN](/Images/VPN%20CTF/VPN_1.png)

## Description

![VPN](/Images/VPN%20CTF/VPN_2.png)

### Unusual Login
Welcome to your first shift! You are greeted by an internal alert on the SOC dashboard titled "Unusual VPN login of susan.martin@probablyfine.thm from 37.19.201.132 (Singapore)."

The SOC handover notes did indeed mention that Susan from Marketing is in Singapore, attending a security vendor conference. It is probably just fine, but the SOC procedure tells us to verify each IP in our threat intel platform TryDetectThis. Answer the first four questions to gather more information and determine the threat level.

### Security Check Tool
That login IP looks suspicious, doesn't it? Your teammates reached out to Susan, and she confirmed she did not log in to the company VPN. She also mentioned that while using a public Wi-Fi hotspot at a cafe, she was suddenly prompted to install a "security check" tool, which she did. The host telemetry reveals a suspicious binary with the hash b8e02f2bc0ffb42e8cf28e37a26d8d825f639079bf6d948f8debab6440ee5630. Can you help us figure out what this binary exactly does and answer the remaining questions?

## Write-up

### 1. Initial investigation and reading

When first reading the description we were put into a position of a SOC analyst who was alerted and given a bunch of info which would help us identify the info/flags needed to be submitted. Among those given was an IP and a binary hash. Which this info we can quickly build up a profile and see what activities/reports are on each values. Thankfully the site has given us a good website to check these values.

![VPN](/Images/VPN%20CTF/VPN_3.png)

The objective is simple which is to trace various info from the 2 sources to try build a case/profile on the supposed malware or its attacker. The sources would feed us bread crumbs to follow and slowly build upon.

### 2. Checking the values

The first thing I wanted to check was the IP address and all its activity, which the site given by THM has. 

![VPN](/Images/VPN%20CTF/VPN_4.1.png)

Here on the dashboard of activity we find various different info coming from this IP and its activity rummaging through the info we can tell that the IP recently was communicating with a file extension with a .virus and various apk applications which could explain the suspicious login but not fully build a profile on what we are investigating.

Checking for what info we need we can find various info needed like ASN number related (1st flag) and what service is offered from the IP (2nd flag).

![VPN](/Images/VPN%20CTF/VPN_4.png)

![VPN](/Images/VPN%20CTF/VPN_5.png)

The next flag brings us to check the hash value upon initial inspection we find the filename of the file related to the hash (3rd flag)

![VPN](/Images/VPN%20CTF/VPN_6.png)

Scrolling down on the vendor analysis it seems that most of it flagged this hash as a malicious software and it would seem we can also find the threat signature that Microsoft assigned to the file (4th flag) which labels it as a trojan.

Just a quick context on what a trojan it is a malware which embeds harmful code in itself behind a legitimate appearance, like how susan described her experience in the cafe where she was prompted to download a "security check" tool which was a trojan and led to this situation. This can often lead to info ripping, spying or opening back doors.

![VPN](/Images/VPN%20CTF/VPN_7.png)

Next on the list we needed to check was how many domains are linked to the same campaign (5th flag)

![VPN](/Images/VPN%20CTF/VPN_7.1.png)

![VPN](/Images/VPN%20CTF/VPN_8.png)

![VPN](/Images/VPN%20CTF/VPN_9.png)

To find this we can screen through the hash source to see its contacted domains we can see 3 of those domains contain malicious services/items within them checking the first domain 'gadgethgfub.icu' leads us to the 5th flags as it list various alternate domain names it uses. Counting it can be a hassle so we copy everything and put it into a word counter to give use the total number of lines which out puts our 5th flag.

The next flag needs us to find the what line is present in the rule's "condition" field? in one of the YARA rules made by "kevoreilly".

![VPN](/Images/VPN%20CTF/VPN_10.png)

![VPN](/Images/VPN%20CTF/VPN_11.png)

![VPN](/Images/VPN%20CTF/VPN_12.png)

To find this we have to head to kevoreilly's github and into the CAPEv2 repo to find the exact file mentioned in the Yara rules which is  'Lumma' finding this exact file we can check the various commits made with this file and inside the latest commit there is a line with a condition which is our 6th flag.