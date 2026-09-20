# PicoCTF Rotation (Medium Cryptography)
![rot13](/Images/Rotation%20CTF//rot_1.png)

## Description

You will find the flag after decrypting this file.

## Write up

### 1. Initial text impression & Finding the Flag

After downloading the file I got a text file with a decrypted text on it. Looking at it on first glance it looked similar to how PicoCTF formats it's flags so this is just a decrypted text which we need to find a way to decrypt.

The easiest way is to load up cyber chef which is like a swiss army knife of decryption allowing users to decrypt any sorts of message with different encoding methods.

My first thought when I saw the title was rotating the text so I dragged the rotate recipe and cycled through but I did not get a hit.

![rot13](/Images/Rotation%20CTF//rot_2.png)

Next thought was to think how else characters can rotate and I thought of rot13 which is a shifting/rotating cipher by 13 places using this logic 13 is a replaceable number and using cyber chef I could increase or decrease the shift/rotation after moving up to 18 I was able to find the flag.

![rot13](/Images/Rotation%20CTF//rot_3.png)

## Flag 🚩
<details>
    <summary>Flag</summary>

    picoCTF{r0tat1on_d3crypt3d_a4b7d759}

</details>

## Reflection

Although this challenge was more relaxing than the others it still provided a little bit of brain stimulation when solving. I also helped me understand cryptology more, some ciphers can be changed the classic rot13 can be modified to provide a challenge to someone who wouldn't have prior knowledge.