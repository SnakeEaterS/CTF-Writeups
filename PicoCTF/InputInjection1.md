# PicoCTF Input Injection 1 (Medium)

![input](/Images/InputInjection1%20CTF/input_1.png)

## Description

A friendly program wants to greet you… but its goodbye might say more than it should. Can you convince it to reveal the flag?

## Write-up 

### 1. Initial Interaction with the CTF

After reading the description and starting the instance I was prompted to connect to the host server where a custom program is being ran on. 

![input](/Images/InputInjection1%20CTF/input_2.png)

Upon testing it, the program asked for a name and ends the interaction and kicks me out of the host server.

### 2. Checking the source code and finding the vulnerability.

I tried testing the program on my command prompt to see if there was any difference but it ran similar to how it did on the host server.

![input](/Images/InputInjection1%20CTF/input_4.png)

Next thing I did was to check the program source code. by downloading the given link.

![input](/Images/InputInjection1%20CTF/input_6.png)

The script given shows the program and how it handles inputs and outputs but what is interesting here is that the function fun which has 2 inputs 'char *name, char *cmd has a char' buffer line which can be exploited by allowing us to run a command after 10 characters. in main this is a large number like 200 so the program can safely read 200 bytes using 'fgets(name, sizeof(name), stdin);'.

But in fun the buffer can only handle 10. Where the vulnerability escalates is in 'strcpy(buffer, name);' the 'strcpy' function does not check for any boundaries of the buffer destination. It will copy characters from 'name' until it reaches a null terminator '\0' Because name can only hold up n to 200 bytes of data, passing anything larger than 9 characters (plus the null terminator) go past the boundaries of buffer[10].

Using this all I have to do is type 10 characters before running a bash line code in this case its 'sh' where it give me a bourne shell allowing me to execute commands where I ls to find whats in the directory and print he flag text file.

![input](/Images/InputInjection1%20CTF/input_7.png)

## Flag 🚩
<details>
    <summary>Flag</summary>

    picoCTF{0v3rfl0w_c0mm4nd_d3eb7161}

</details>

## Prevention 🔐

### 1. Match the buffer sizes across the 2 functions.

Have a constant variable which handles buffer sizes across different functions. Doing this reduces the chance of the attacker guessing/writing any executables. but doing this does not completely stop attackers.

### 2. Fundamental change is switching from strcpy to strncpy 

This change will allow the program to only read whats within the buffer so lets say 200 it will only read the first 200 characters and not allowing anything pass to slip into adjacent memory or overwrite the c variable allowing for command execution within a interactive shell.

### 3. Use strict input validation 

The code should always check if the input length is 'out of bounds' meaning if it passes the buffer rejecting it if it passed. This works as it can prevent any malicious or accidental big inputs at the program before they can reach dangerous operations like string copy functions.

## What I learned 
This challenge helped me identify a minor security flaw which can lead to a compromised system. This shows how legacy functions and poor architecture could create vulnerabilities that attackers can exploit.

Skills practiced:
- Vulnerability Identification
- Basic Network Interaction
- Arbitrary Command Execution
- Unsafe Function Awareness



