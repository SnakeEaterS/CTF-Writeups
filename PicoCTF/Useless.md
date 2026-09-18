# PicoCTF Useless Write-up (Medium)

![Useless](/Images/Useless%20CTF/Useless1.png)

## Description
There's an interesting script in the user's home directory

## Write-up

### 1. Logging into the SSH Shell and checking the directory

Upon starting the CTF instance I was prompted to log into the SSH. Once in the shell I did ls to see what I was working with, within the directory I was placed in there was a singular script named "useless". 

Wanting to know what the script does I did "man useless" to check the documentation if it has any before reading the script itself. Within the documentation the script seems to be a simple calculator script that handles addition, multiplication, subtraction and division.

Scrolling down more within the Authors section there is a flag.

![Useless](/Images/Useless%20CTF/Useless2.png)

## Useless Flag 🚩
<details>
    <summary>Flag</summary>

    picoCTF{use3l3ss_ch4ll3ng3_3xpl0it3d_4151}

</details>

## Reflection
I did not expect it to be simple. Although it was simple it showed my thought process when attempting CTFs to read/know every little thing about the challenge. Overall good experience.
