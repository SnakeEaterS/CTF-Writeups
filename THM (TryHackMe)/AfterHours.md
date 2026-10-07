# TryHackMe After Hours (Medium) (Forensics)

![After](/Images/AfterHours%20CTF/AfterHours_1.png)

## Description

![After](/Images/AfterHours%20CTF/AfterHours_2.png)

![After](/Images/AfterHours%20CTF/AfterHours_3.png)

### 🛎️ Concierge Briefing
Long after the front desk closes and the pool lights dim, the resort's back-office machines keep humming. Someone, or something, has been logging in during the small hours, well after the night-shift technician has gone home.

Nothing obvious shows up in Startup, Scheduled Tasks, or the registry Run keys. Whatever's keeping itself alive is hiding somewhere quieter, tucked away in a corner of the system most tools don't think to check.

## Write-up

Once I extracted the files I was given 5 files to work with. At first glance at these files I would assume the extensions are related to the files. But just in case I wanted to check the what type of file im working with first so I went to check them by using the 'file *' command to give me each file type.  

![After](/Images/AfterHours%20CTF/AfterHours_4.png)

![After](/Images/AfterHours%20CTF/AfterHours_5.png)

After checking I was shown that all of the files were .data files. However, I have not encountered any of these files before so I went to google to get a better understanding of what the files could represent judging that it has specific naming conventions so it would point me to some where.

![After](/Images/AfterHours%20CTF/AfterHours_6.png)

![After](/Images/AfterHours%20CTF/AfterHours_7.png)

![After](/Images/AfterHours%20CTF/AfterHours_8.png)

after doing a bit of research I found that the files represent a WMI repo database (Windows Management Instrumentation) basically it stores class definitions, schemas and static config data. Generally investigators when they do forensics work on the WMI it would show them any attacker persistence or a custom WMI classes which is like hiding files in the database. 

So my plan now is to find a way to read the data stored inside I found a git hub repo which allows me to run a command to read the object.data file which is usually used to store the data and where I should start if I want to find any clues.
```
Repo : https://github.com/davidpany/WMI_Forensics 
```
![After](/Images/AfterHours%20CTF/AfterHours_9.png)

![After](/Images/AfterHours%20CTF/AfterHours_10.png)

Once I downloaded the tool I ran the command to search for any keywords that might be in the db. However, I was not able to get any interesting hits most of the keywords looks like normal window process. To pivot, I opted to using the command string to the entire database to get all the readable text and grep it with keywords like flag.

![After](/Images/AfterHours%20CTF/AfterHours_11.png)

![After](/Images/AfterHours%20CTF/AfterHours_12.png)

After doing this a couple of times without finding any hits I did more research on what the object.data file could contain other than files. I found that while OBJECTS.DATA doesn't log everyday shell history, it does store permanent WMI tasks. By carving the binary database for the keyword powershell, I found a interesting WMI Event Consumer. I found a hidden, encoded PowerShell script directly into the WMI repository. This allows the script to remain hidden from standard file scanners and execute automatically in the background whenever its associated system trigger fires.

![After](/Images/AfterHours%20CTF/AfterHours_13.png)

Looking at the encoding it looks to be a base64 code since it has a '=' at the end so opening up cyber chef to decode it I manage to get a script which looks to be a reflective assembly loading script this is to bypass physical disk monitoring if a person wants to hide something. The interesting line here is at the top where it gets a hidden payload under 'Win32_HardwareTelemetry' pointing to where to look next. 

![After](/Images/AfterHours%20CTF/AfterHours_14.png)

![After](/Images/AfterHours%20CTF/AfterHours_15.png)

![After](/Images/AfterHours%20CTF/AfterHours_16.png)

Reusing the search technique of stringing the object file and grepping it I was able to find another base64 encoded string where I pasted into cyber chef with a different recipe since the original output was compressed. The output was interesting as at the top is has the header MZ which could mean its a executable file. Cyber chef has a built in output downloader so I downloaded the output as a exe file.

![After](/Images/AfterHours%20CTF/AfterHours_17.png)

![After](/Images/AfterHours%20CTF/AfterHours_18.png)

Next I wanted to check what the file type is after using the command 'file *' it shows  '.NET assembly' which I am able to decompile and read by using ILSpy a open source .NET decompiler to read the internals of the executable. 

After loading the executable into my ILspy decompiler I was able to see the program within this file where when executed it opens command prompt and creates a 'backdoor' or a new user with a password where again the password is in base64 where the attacker could possibly access.

User: Patch

Password: VEHe1A0dGNoX29wM25lZF90aDNFqmFjS2QwMHJ9

This whole method of using a executable to create a back door it called  Target Environmental Guardrails and Account Creation for Persistence.

Looking at the code it seems that the target of this attack was specifically the 'bytelotusdc' machine. This is probably to evade any malware analysis on a sandbox environment as it would only target 'bytelotusdc' which is to hide its intent and exit cleanly on a test environment.

Then If the condition is met it runs and creates a backdoor for the attacker by using the command prompt to exploit and gain access to the machine creating persistence.

![After](/Images/AfterHours%20CTF/AfterHours_19.png)

Copying and pasting the file into the cyber chef and decoding using base64 gives us the flag.

## Flag 🚩
<details>
    <summary>Flag</summary>

    THM{p4tch_o3pned_th3_Backd00r}

</details>

## What I learned

The entire process taught me how to complete a Advanced Threat Hunting and Memory Forensics Lifecycle. Where I started with raw data within a database blob and slowly uncover each layer of the exploit evasion defenses to expose a active targeted threat. 

Skills practiced:

- WMI Database Carving & Analysis
- Malware Persistence Hunting
- Defeating Signature-Based Detection
- Source Code Auditing
- Static Reverse Engineering
- In-Memory Injection Mechanics
- Environmental Guardrails
- Malware Infrastructure Hiding

<sub>Documentation done on 7/10/26</sub>

