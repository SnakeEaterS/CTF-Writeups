# TryHackMe Towel On The Sunbed (Medium)

![Towel](/Images/Towel%20CTF/Towel_1.png)

## Description

![Towel](/Images/Towel%20CTF/Towel_2.png)

![Towel](/Images/Towel%20CTF/Towel_3.png)

### 🛎️ Concierge Briefing

Ponzi found the resort's wellness portal running a little side project called Ponzi — a crypto rewards app, poolside edition. He set his towel down, claimed his daily reward, and went to reapply sunscreen. He came back to find the sunbed had been "claimed" three times over while he wasn't looking.

He's convinced the app owes him a spot in the Whale Vault. The app disagrees, politely, once every 24 hours. Somewhere between his request and the server's clock, there's a gap wide enough to walk a whale through.

## Write-up

### 1. Initial Recon

Entering the IP:3000 on to the browser I was greeted with a login page along with a register page. The first thing I tried was the admin admin to see if there was any admin account I could use.

![Towel](/Images/Towel%20CTF/Towel_4.png)

Next was to check the page source code for anything interesting like dev notes or a hidden url path that I can enumerate into.

![Towel](/Images/Towel%20CTF/Towel_5.png)

Without finding anything useful I opted to make a account. While making a account I found that the username guest was taken which is interesting maybe I can try to get into that account but for now I just wanted to see the web page after the login.

![Towel](/Images/Towel%20CTF/Towel_6.png)

When creating the account and logging in I was greeted to something like a crypto wallet site with the various prices of each crypto currently. 

The interesting thing about this page is that it has a reward claim section where you can earn 50 points daily.

![Towel](/Images/Towel%20CTF/Towel_7.png)

When claiming I was given 50 points and given a timer to the next free claim.

![Towel](/Images/Towel%20CTF/Towel_8.png)

![Towel](/Images/Towel%20CTF/Towel_10.png)

Next thing I did was to scan the ports to see what they have listening and I found a SSH listening on 22/tcp and http listening on 3000/tcp which is the website. Nothing interesting as there is no input for a injection except the username and password which filters symbols for a SQL injection.

![Towel](/Images/Towel%20CTF/Towel_9.png)

### 2. Uncovering the vulnerability

What I wanted to check was the burp suite for any interesting POST/GET request. specifically anything that can either give me ssh access or exploit the reward claim to see the special prize as it is mostly pointing towards that area

Since I already claimed the reward on this account and instead of waiting out the 24 hours I decided to create a new test account for this. First I logged in and claimed the reward to test and view the POST request to find anything exploitable.

![Towel](/Images/Towel%20CTF/Towel_11.png)

![Towel](/Images/Towel%20CTF/Towel_12.png)

While checking the POST request for the reward claim I could see a connect.id cookie which basically identifies a user's session. This gave me an idea if I could parse another session token into my original post request and claim it on a single account.

![Towel](/Images/Towel%20CTF/Towel_13.png)

![Towel](/Images/Towel%20CTF/Towel_14.png)

![Towel](/Images/Towel%20CTF/Towel_15.png)

![Towel](/Images/Towel%20CTF/Towel_16.png)

Unfortunately it didn't work as it would just claim it for the other user the reason this is the case is I tired doing whats called a session hijacking where I take a user session token and replace it on my browser allowing me to bypass the login and authentication. However, doing this method like this where I just replace the session token in the POST request it would just claim it for that session.

![Towel](/Images/Towel%20CTF/Towel_16.1.png)

![Towel](/Images/Towel%20CTF/Towel_17.png)




