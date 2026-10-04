# TryHackMe After Hours (Medium) (Forensics)

![After](/Images/AfterHours%20CTF/AfterHours_1.png)

## Description

![After](/Images/AfterHours%20CTF/AfterHours_2.png)

![After](/Images/AfterHours%20CTF/AfterHours_3.png)

### 🛎️ Concierge Briefing
Long after the front desk closes and the pool lights dim, the resort's back-office machines keep humming. Someone, or something, has been logging in during the small hours, well after the night-shift technician has gone home.

Nothing obvious shows up in Startup, Scheduled Tasks, or the registry Run keys. Whatever's keeping itself alive is hiding somewhere quieter, tucked away in a corner of the system most tools don't think to check.

## Write-up

### 1. viewing and researching

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

Once I downloaded the tool I ran the command to search for any keywords that might be in the db. However, I was not able to get any interesting hits most of the keywords looks like normal window process. To pivot, i opted to using the command string to the entire database to get all the readable text and grep it with keywords like flag.

![After](/Images/AfterHours%20CTF/AfterHours_11.png)

![After](/Images/AfterHours%20CTF/AfterHours_12.png)

After doing this a couple of times without finding any hits I did more research on what the object.data file could contain other than files

![After](/Images/AfterHours%20CTF/AfterHours_13.png)