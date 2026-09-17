# PicoCTF DISKO4 Write-up (Medium)
![DISKO4 Description](/Images/DISKO4%20CTF/DISKO4_1.png)

## Description 
Can you find the flag in this disk image? This time I deleted the file! Let see you get it now!

## Write-up

### 1. Downloading the file

Clicking the link starts a download of a .gz file named "disko-4.dd.gz".

Seeing as it has the extension .gz its a Gzip file so I ran the command "gunzip disko-4.dd.gz" to get a .dd file which is basically a disk image where I can look through.

After getting the .dd file I took a peek inside using 7z command which allows me to see the files inside similar to like how you can see within a zip file since a .dd file is just a raw uncompressed disk image.

![DISKO4 hi](/Images/DISKO4%20CTF/DISKO4_2.png)

### 2. Extracting the contents

Skimming the contents it seems like its a log folder of a system where it has records of events of the computer which is interesting as looking back to the description of the CTF we are finding a deleted file of sorts so the info provided here could give us some clues.

![DISKO4 hi](/Images/DISKO4%20CTF/DISKO4_3.png)

Next I wanted to read the files within the disk image so i extracted the contents with 7z and put it all into a folder named "hi"

This is what it contains:
![DISKO4 hi](/Images/DISKO4%20CTF/DISKO4_3.1.png)

### 3. Searching the contents

Skimming through the files mostly the APT and dpkg.log files it seems the flag file is burried in massive ammounts of software purges as greping "Remove" shows alot of tools/software being removed.

This is supported by reading the apt history logs where the user had ran a command to remove a bunch of software and tools.

![DISKO4 hi](/Images/DISKO4%20CTF/DISKO4_3.2.png)
![DISKO4 hi](/Images/DISKO4%20CTF/DISKO4_3.3.png)

After looking abit more to find some low-level file removal logs I was not able to find anything related to the CTF.

But if they gave the disk image that means the file removal was done within the image or at least within the logs folder.

### 4. Checking the metadata structure itself

Doing a little research to see how to find deleted files from a disk image/.dd file I found a way to see any files removed within the disk images.

I found that using the fls command which is part of the sleuthkit a list of command-line tools used for analyzing disk images and recovering files. The specific command reads the file system metadata structure to find any deleted files.

![DISKO4 hi](/Images/DISKO4%20CTF/DISKO4_4.png)

by using -r & -d which searches the entire disk image and all its subdirectories to find any deleted files I managed to find 1 deleted directory and 1 deleted .gz file along with their respective inode number/record number which is useful for later.

Next is some how extracting the deleted files. Thankfully the sleuthkit has a command just for that by using the icat command which extracts files based on its inode/record number then reads its corresponding metadata to reconstruct the file. Downsides to this is fragmenting large/frequently modified files cant be reconstructed with this command as they are usually scattered across different sections of the drive rather than stored in one continuous piece.

Using this info I started extracting the deleted files I started with the directory but got back a error which says "Error recovering deleted file (Invaild address in run (Too Large): 204800)" Probably ran into the issue of fragmenting since its a directory and has multiple files within it making hard to reconstruct.

![DISKO4 hi](/Images/DISKO4%20CTF/DISKO4_5.png)

### 5.Finding the flag

Next step was to extract the .gz file that was deleted judging by its name we are probably going the right direction. So I ran the icat command again to get the file.

Had some issues with the naming conventions since I have a same file name. But once I managed to get the .gz file I used gunziped it to get a file with no extension. Then I read its contents and found the flag 🚩

![DISKO4 hi](/Images/DISKO4%20CTF/DISKO4_6.png)

## DISKO4 Flag 🚩
<details>
    <summary>Flag</summary>

    picoCTF{d3l_d0n7_h1d3_w3ll_c2fcb641}

</details>

