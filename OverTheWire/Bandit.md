###### SSH Credentials
> User: bandit0  
> Password: bandit0  
> Address: bandit.labs.overthewire.org   
> Port: 2220  

### Bandit 0
###### The password for the next level is stored in a file called readme located in the home directory.      

**cat** readme
> boJ9jbbUNNfktd78OOpsqOltutMc3MY1
##

### Bandit 1
###### The password for the next level is stored in a file called - located in the home directory.  
**cat** ./-
> CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9
##

### Bandit 2
###### The password for the next level is stored in a file called spaces in this filename located in the home directory.  
**cat** spaces\ in\ this\ filename
> UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK
##

### Bandit 3
###### The password for the next level is stored in a hidden file in the inhere directory.  
**cat** inhere/.hidden
> pIwrPrtPN36QITSp3EQaw936yaFoFgAB
##

### Bandit 4
###### The password for the next level is stored in the only human-readable file in the inhere directory.
We can use the **find** command to look for human readable files.  
**find** . -type f | **xargs** file | **grep** text  

Only the file -file07 has ASCII text.  
**cat** ./-file07  
>koReBOKuIDDepwhWk7jZC0RTdopnAYKh
##

### Bandit 5
###### The password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties:

    human-readable
    1033 bytes in size
    not executable  

**find** inhere/ -type f -size 1033c | **xargs** file | **grep** text
> Result: inhere/maybehere07/.file2: ASCII text, with very long lines  

**cat** inhere/maybehere07/.file2
> DXjZPULLxYr17uwoI01bNLQbtFemEgo7
##

### Bandit 6
###### The password for the next level is stored somewhere on the server and has all of the following properties:

    owned by user bandit7
    owned by group bandit6
    33 bytes in size  

**find** / -type f -size 33c -user bandit7 -group bandit6 | **xargs** file | **grep** text   
> Result: (among others) /var/lib/dpkg/info/bandit7.password: ASCII text

**cat** /var/lib/dpkg/info/bandit7.password
> HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs
##

### Bandit 7
###### The password for the next level is stored in the file data.txt next to the word millionth.

**cat** data.txt | **grep** millionth
>millionth	cvX2JJa4CFALtqS87jk27qwqGhBM9plV
##

### Bandit 8
###### The password for the next level is stored in the file data.txt and is the only line of text that occurs only once.  

**sort** data.txt | **uniq** -u
> UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
##

### Bandit 9
###### The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several ‘=’ characters.

**strings** data.txt | **grep** =
> (Among others) &========== truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
##

### Bandit 10
###### The password for the next level is stored in the file data.txt, which contains base64 encoded data.

**base64** -d data.txt 
> The password is IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR
##

### Bandit 11
###### The password for the next level is stored in the file data.txt, where all letters(A-Za-z) have been rotated by 13 positions.  

**cat** data.txt | **tr** 'A-Za-z' 'N-ZA-Mn-za-m'  
> The password is 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu
##

### Bandit 12
###### The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed.  

```
00000000: 1f8b 0808 0650 b45e 0203 6461 7461 322e  .....P.^..data2.
00000010: 6269 6e00 013d 02c2 fd42 5a68 3931 4159  bin..=...BZh91AY
00000020: 2653 598e 4f1c c800 001e 7fff fbf9 7fda  &SY.O...........
00000030: 9e7f 4f76 9fcf fe7d 3fff f67d abde 5e9f  ..Ov...}?..}..^.
00000040: f3fe 9fbf f6f1 feee bfdf a3ff b001 3b1b  ..............;.
00000050: 5481 a1a0 1ea0 1a34 d0d0 001a 68d3 4683  T......4....h.F.
00000060: 4680 0680 0034 1918 4c4d 190c 4000 0001  F....4..LM..@...
00000070: a000 c87a 81a3 464d a8d3 43c5 1068 0346  ...z..FM..C..h.F
00000080: 8343 40d0 3400 0340 66a6 8068 0cd4 f500  .C@.4..@f..h....
00000090: 69ea 6800 0f50 68f2 4d00 680d 06ca 0190  i.h..Ph.M.h.....
000000a0: 0000 69a1 a1a0 1ea0 194d 340d 1ea1 b280  ..i......M4.....
000000b0: f500 3406 2340 034d 3400 0000 3403 d400  ..4.#@.M4...4...
000000c0: 1a07 a832 3400 f51a 0003 43d4 0068 0d34  ...24.....C..h.4
000000d0: 6868 f51a 3d43 2580 3e58 061a 2c89 6bf3  hh..=C%.>X..,.k.
000000e0: 0163 08ab dc31 91cd 1747 599b e401 0b06  .c...1...GY.....
000000f0: a8b1 7255 a3b2 9cf9 75cc f106 941b 347a  ..rU....u.....4z
00000100: d616 55cc 2ef2 9d46 e7d1 3050 b5fb 76eb  ..U....F..0P..v.
00000110: 01f8 60c1 2201 33f0 0de0 4aa6 ec8c 914f  ..`.".3...J....O
00000120: cf8a aed5 7b52 4270 8d51 6978 c159 8b5a  ....{RBp.Qix.Y.Z
00000130: 2164 fb1f c26a 8d28 b414 e690 bfdd b3e1  !d...j.(........
00000140: f414 2f9e d041 c523 b641 ac08 0c0b 06f5  ../..A.#.A......
00000150: dd64 b862 1158 3f9e 897a 8cae 32b0 1fb7  .d.b.X?..z..2...
00000160: 3c82 af41 20fd 6e7d 0a35 2833 41bd de0c  <..A .n}.5(3A...
00000170: 774f ae52 a1ac 0fb2 8c36 ef58 537b f30a  wO.R.....6.XS{..
00000180: 1510 cab5 cb51 4231 95a4 d045 b95c ea09  .....QB1...E.\..
00000190: 9fa0 4d33 ba43 22c9 b5be d0ea eeb7 ec85  ..M3.C".........
000001a0: 59fc 8bf1 97a0 87a5 0df0 7acd d555 fc11  Y.........z..U..
000001b0: 223f fdc6 2be3 e809 c974 271a 920e acbc  "?..+....t'.....
000001c0: 0de1 f1a6 393f 4cf5 50eb 7942 86c3 3d7a  ....9?L.P.yB..=z
000001d0: fe6d 173f a84c bb4e 742a fc37 7b71 508a  .m.?.L.Nt*.7{qP.
000001e0: a2cc 9cf1 2522 8a77 39f2 716d 34f9 8620  ....%".w9.qm4.. 
000001f0: 4e33 ca36 eec0 cd4b b3e8 48e4 8b91 5bea  N3.6...K..H...[.
00000200: 01bf 7d21 0b64 82c0 3341 3424 e98b 4d7e  ..}!.d..3A4$..M~
00000210: c95c 1b1f cac9 a04a 1988 43b2 6b55 c6a6  .\.....J..C.kU..
00000220: 075c 1eb4 8ecf 5cdf 4653 064e 84da 263d  .\....\.FS.N..&=
00000230: b15b bcea 7109 5c29 c524 3afc d715 4894  .[..q.\).$:...H.
00000240: 7426 072f fc28 ab05 9603 b3fc 5dc9 14e1  t&./.(......]...
00000250: 4242 393c 7320 98f7 681d 3d02 0000       BB9<s ..h.=...
```  
We revert the data.txt back into binary.  
**xxd** -r data.txt data_reverted.txt  

Using **file** command, we check the file format of data_reverted.txt, which is a .gzip compressed data.  
We'll convert each file to the right format and extract its content by using **mv** [file] [file.NewFormat]  

* For gzip, I used **gzip** -d [filename.gz] to extract the data.  
* For bzip2, I used **bzip2** -d [filename.bz2] to extract the data.  
* For tar, I used **tar** -xf [filename.tar] to extract the data.  

**Steps**: txt &#8594; binary &#8594; .gz &#8594; .bz2 &#8594; .gz &#8594; .tar &#8594; .tar &#8594; .bz2 &#8594; .tar &#8594; .gz ==> TXT.  
> The password is 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL
##

### Bandit 13
###### The password for the next level is stored in /etc/bandit_pass/bandit14 and can only be read by user bandit14.  
###### For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level.   

```
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAxkkOE83W2cOT7IWhFc9aPaaQmQDdgzuXCv+ppZHa++buSkN+
gg0tcr7Fw8NLGa5+Uzec2rEg0WmeevB13AIoYp0MZyETq46t+jk9puNwZwIt9XgB
ZufGtZEwWbFWw/vVLNwOXBe4UWStGRWzgPpEeSv5Tb1VjLZIBdGphTIK22Amz6Zb
ThMsiMnyJafEwJ/T8PQO3myS91vUHEuoOMAzoUID4kN0MEZ3+XahyK0HJVq68KsV
ObefXG1vvA3GAJ29kxJaqvRfgYnqZryWN7w3CHjNU4c/2Jkp+n8L0SnxaNA+WYA7
jiPyTF0is8uzMlYQ4l1Lzh/8/MpvhCQF8r22dwIDAQABAoIBAQC6dWBjhyEOzjeA
J3j/RWmap9M5zfJ/wb2bfidNpwbB8rsJ4sZIDZQ7XuIh4LfygoAQSS+bBw3RXvzE
pvJt3SmU8hIDuLsCjL1VnBY5pY7Bju8g8aR/3FyjyNAqx/TLfzlLYfOu7i9Jet67
xAh0tONG/u8FB5I3LAI2Vp6OviwvdWeC4nOxCthldpuPKNLA8rmMMVRTKQ+7T2VS
nXmwYckKUcUgzoVSpiNZaS0zUDypdpy2+tRH3MQa5kqN1YKjvF8RC47woOYCktsD
o3FFpGNFec9Taa3Msy+DfQQhHKZFKIL3bJDONtmrVvtYK40/yeU4aZ/HA2DQzwhe
ol1AfiEhAoGBAOnVjosBkm7sblK+n4IEwPxs8sOmhPnTDUy5WGrpSCrXOmsVIBUf
laL3ZGLx3xCIwtCnEucB9DvN2HZkupc/h6hTKUYLqXuyLD8njTrbRhLgbC9QrKrS
M1F2fSTxVqPtZDlDMwjNR04xHA/fKh8bXXyTMqOHNJTHHNhbh3McdURjAoGBANkU
1hqfnw7+aXncJ9bjysr1ZWbqOE5Nd8AFgfwaKuGTTVX2NsUQnCMWdOp+wFak40JH
PKWkJNdBG+ex0H9JNQsTK3X5PBMAS8AfX0GrKeuwKWA6erytVTqjOfLYcdp5+z9s
8DtVCxDuVsM+i4X8UqIGOlvGbtKEVokHPFXP1q/dAoGAcHg5YX7WEehCgCYTzpO+
xysX8ScM2qS6xuZ3MqUWAxUWkh7NGZvhe0sGy9iOdANzwKw7mUUFViaCMR/t54W1
GC83sOs3D7n5Mj8x3NdO8xFit7dT9a245TvaoYQ7KgmqpSg/ScKCw4c3eiLava+J
3btnJeSIU+8ZXq9XjPRpKwUCgYA7z6LiOQKxNeXH3qHXcnHok855maUj5fJNpPbY
iDkyZ8ySF8GlcFsky8Yw6fWCqfG3zDrohJ5l9JmEsBh7SadkwsZhvecQcS9t4vby
9/8X4jS0P8ibfcKS4nBP+dT81kkkg5Z5MohXBORA7VWx+ACohcDEkprsQ+w32xeD
qT1EvQKBgQDKm8ws2ByvSUVs9GjTilCajFqLJ0eVYzRPaY6f++Gv/UVfAPV4c+S0
kAWpXbv5tbkkzbS0eaLPTKgLzavXtQoTtKwrjpolHKIHUz6Wu+n4abfAIRFubOdN
/+aLoRQ0yBDRbdXMsZN/jvY44eM+xRLdRVyMmdPtP8belRi2E2aEzA==
-----END RSA PRIVATE KEY-----
```

Copy the RSA private key from the remote machine to the attacking machine.  

**chmod** 600 rsa_file &#8594; set the read & write permission for the owner of the file.  

SSH into the next level: **ssh** -i rsa_file bandit14@bandit.labs.overthewire.org -p 2220.  

> Content of /etc/bandit_pass/bandit14: 4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e  
##

### Bandit 14
###### The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.  

**echo** "4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e" | nc localhost 30000  
> BfMYroe26WYalil77FoDi9qh59eK5xNr
##

### Bandit 15
###### The password for the next level can be retrieved by submitting the password of the current level to port 30001 using SSL encryption.

**echo** "BfMYroe26WYalil77FoDi9qh59eK5xNr" | **openssl** s_client -connect localhost:30001 -ign_eof  
* ign_eof is used to keep the connection open when the end of file is reached in the input.  
> cluFn7wTiGryunymYOu4RcffSxQluehd
##

### Bandit 16
###### The credentials for the next level can be retrieved by submitting the password of the current level to a port on localhost in the range 31000 to 32000.There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.  

**nmap** localhost -p31000-32000 -script=ssl-enum-ciphers -sV

After the scan has completed, we see that only 2/5 open ports have SSL. 

```
31518/tcp open  ssl/echo
| ssl-enum-ciphers: 
|   TLSv1.0: 
|     ciphers: 
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_CAMELLIA_128_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_CAMELLIA_256_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_SEED_CBC_SHA (rsa 1024) - A
|     compressors: 
|       NULL
|     cipher preference: client
|     warnings: 
|       Weak certificate signature: SHA1
|   TLSv1.1: 
|     ciphers: 
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_CAMELLIA_128_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_CAMELLIA_256_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_SEED_CBC_SHA (rsa 1024) - A
|     compressors: 
|       NULL
|     cipher preference: client
|     warnings: 
|       Weak certificate signature: SHA1
|   TLSv1.2: 
|     ciphers: 
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_CAMELLIA_128_CBC_SHA256 (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_CAMELLIA_256_CBC_SHA384 (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256 (rsa 1024) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA256 (rsa 1024) - A
|       TLS_RSA_WITH_AES_128_CCM (rsa 1024) - A
|       TLS_RSA_WITH_AES_128_CCM_8 (rsa 1024) - A
|       TLS_RSA_WITH_AES_128_GCM_SHA256 (rsa 1024) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA256 (rsa 1024) - A
|       TLS_RSA_WITH_AES_256_CCM (rsa 1024) - A
|       TLS_RSA_WITH_AES_256_CCM_8 (rsa 1024) - A
|       TLS_RSA_WITH_AES_256_GCM_SHA384 (rsa 1024) - A
|       TLS_RSA_WITH_CAMELLIA_128_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_CAMELLIA_128_CBC_SHA256 (rsa 1024) - A
|       TLS_RSA_WITH_CAMELLIA_256_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_CAMELLIA_256_CBC_SHA256 (rsa 1024) - A
|       TLS_RSA_WITH_SEED_CBC_SHA (rsa 1024) - A
|     compressors: 
|       NULL
|     cipher preference: client
|     warnings: 
|       Weak certificate signature: SHA1
|_  least strength: A
```
### AND  
```
31790/tcp open  ssl/unknown
| fingerprint-strings: 
|   FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, Kerberos, LDAPSearchReq, LPDString, RTSPRequest, SIPOptions, SSLSessionReq, TLSSessionReq: 
|_    Wrong! Please enter the correct current password
| ssl-enum-ciphers: 
|   TLSv1.0: 
|     ciphers: 
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_CAMELLIA_128_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_CAMELLIA_256_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_SEED_CBC_SHA (rsa 1024) - A
|     compressors: 
|       NULL
|     cipher preference: client
|     warnings: 
|       Weak certificate signature: SHA1
|   TLSv1.1: 
|     ciphers: 
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_CAMELLIA_128_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_CAMELLIA_256_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_SEED_CBC_SHA (rsa 1024) - A
|     compressors: 
|       NULL
|     cipher preference: client
|     warnings: 
|       Weak certificate signature: SHA1
|   TLSv1.2: 
|     ciphers: 
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_CAMELLIA_128_CBC_SHA256 (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_CAMELLIA_256_CBC_SHA384 (rsa 1024) - A
|       TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256 (rsa 1024) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA256 (rsa 1024) - A
|       TLS_RSA_WITH_AES_128_CCM (rsa 1024) - A
|       TLS_RSA_WITH_AES_128_CCM_8 (rsa 1024) - A
|       TLS_RSA_WITH_AES_128_GCM_SHA256 (rsa 1024) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA256 (rsa 1024) - A
|       TLS_RSA_WITH_AES_256_CCM (rsa 1024) - A
|       TLS_RSA_WITH_AES_256_CCM_8 (rsa 1024) - A
|       TLS_RSA_WITH_AES_256_GCM_SHA384 (rsa 1024) - A
|       TLS_RSA_WITH_CAMELLIA_128_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_CAMELLIA_128_CBC_SHA256 (rsa 1024) - A
|       TLS_RSA_WITH_CAMELLIA_256_CBC_SHA (rsa 1024) - A
|       TLS_RSA_WITH_CAMELLIA_256_CBC_SHA256 (rsa 1024) - A
|       TLS_RSA_WITH_SEED_CBC_SHA (rsa 1024) - A
|     compressors: 
|       NULL
|     cipher preference: client
|     warnings: 
|       Weak certificate signature: SHA1
|_  least strength: A
```
The port **31790** seems to require some kind of password.
```
fingerprint-strings: 
|   FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, Kerberos, LDAPSearchReq, LPDString, RTSPRequest, SIPOptions, SSLSessionReq, TLSSessionReq:fingerprint-strings: 
|   FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, Kerberos, LDAPSearchReq, LPDString, RTSPRequest, SIPOptions, SSLSessionReq, TLSSessionReq: 
|_    Wrong! Please enter the correct current password
```
**echo** "cluFn7wTiGryunymYOu4RcffSxQluehd" | openssl s_client -connect localhost:31790 -ign_eof
```
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----
```
##

### Bandit 17
###### The password is in passwords.new and is the only line that has been changed between passwords.old and passwords.new.

**diff** -n passwords.old passwords.new
>kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd
##

### Bandit 18
###### Unfortunately, someone has modified .bashrc to log you out when you log in with SSH.  

We can't connect to this level, but we have access to Bandit 18's home directory as Bandit 17.    
If we list the files inside Bandit 18's home directory, there's a readme that we don't have permission to read as bandit 17.  

**ssh** bandit18@bandit.labs.overthewire.org -p 2220 "cat ~/readme"  
> IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x
##

### Bandit 19
###### To gain access to the next level, you should use the setuid binary in the homedirectory. 

We have no clue what this binary is doing.  
The source code seems to be encrypted but we have some important information mixed up with the nonsense.  
```
�����[�Run a command as another user.
```

**./bandit20-do** cat /etc/bandit_pass/bandit20
> GbKksEFF4yrVs6il55v6gwY5aVje5f0j
##

### Bandit 20
###### There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

Connect twice to the machine. On the first terminal, start a netcat listener on any port.  
**nc** -lvp [port]  

On the secound terminal, run the script on the same port.     

After the connection is established, type the current level password in netcat.
> gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr

##

### Bandit 21
###### Look in /etc/cron.d/ for the configuration and see what command is being executed.

**cat** /etc/cron.d/cronjob_bandit22
```
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```

**cat** /usr/bin/cronjob_bandit22.sh
```
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```
**cat** /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
> Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI
##

### Bandit 22
######  Look in /etc/cron.d/ for the configuration and see what command is being executed.

**cat** /usr/bin/cronjob_bandit23.sh
```
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

We see that this simple bash script is using md5 to encrypt a string.  
Each string in the format of "I am user [name]" is encrypted and a 128-bit hash value is generated.  
Then, the user's password is copied to a file located in /tmp/ and named after the generated hash value (/tmp/HashGeneratedValue)  

**echo** "I am user bandit22" | md5sum | cut -d ' ' -f 1 generates the 8169b67bd894ddbb4412f91573b38db3 hash value.  
**cat** /tmp/8169b67bd894ddbb4412f91573b38db3  
> Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI - the password of the current level.   

We can find the password of user bandit23 using the same method as above.
> jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n
##

### Bandit 23
###### Look in /etc/cron.d/ for the configuration and see what command is being executed.  
```
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname
echo "Executing and deleting all scripts in /var/spool/$myname:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done
```
From the script above we see that every script located in /var/spool/username will be executed and deleted after 60 seconds.  

We can create a simple bash script to copy the password from /etc/bandit_pass/bandit24 to another file.  

I created a directory inside /tmp/ and made two files.  
* The first file I created was called script.sh and is a simple bash script.  
```
#!/bin/bash

cat /etc/bandit_pass/bandit24 > /tmp/blablablatemp/pass
```
* The second file I created is an empty file where the password will be written.  

I set the **pass** file **read** and **write** permissions. (Couldn't get it working with only write permission.)  
I set full permissions for the **script.sh** file.  

Copy the script from the /tmp directory to **/var/spool/bandit24**.  

After the script is executed, we get the password for the next level. 

> UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ

**Note**: You can get the password for Bandit 24 while still in Bandit 22 level, by using the same method described there.  
It doesn't seems to work for other levels. 
##

### Bandit 24
###### A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations.

I tried creating a bash script to generate the "password code" string & send it right away, 10.000 times.  
It was too fast for the daemon to keep up even with 1s sleep time. That would be more than 2.77 hours.  

So I created a bash script to generate a list of "password code" strings and then read from that list via netcat.  

Bash script:
```
#!/bin/bash

for i in {0000..9999}
do
    echo "UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ $i" >> password_list.txt
done
```
The password_list.txt is looking like this:
```
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 0000
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 0001
.....
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 9999
```

**cat** password_list.txt | **nc** localhost 30002

After a few seconds and errors because of the wrong pincode, we get our password.  
> The password of user bandit25 is uNG9O58gUE7snukf3bvZ0rxhtnjzSGzG
##

### Bandit 25
###### The shell for user bandit26 is not /bin/bash, but somethipng else. Find out what it is, how it works and how to break out of it.
```
-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEApis2AuoooEqeYWamtwX2k5z9uU1Afl2F8VyXQqbv/LTrIwdW
pTfaeRHXzr0Y0a5Oe3GB/+W2+PReif+bPZlzTY1XFwpk+DiHk1kmL0moEW8HJuT9
/5XbnpjSzn0eEAfFax2OcopjrzVqdBJQerkj0puv3UXY07AskgkyD5XepwGAlJOG
xZsMq1oZqQ0W29aBtfykuGie2bxroRjuAPrYM4o3MMmtlNE5fC4G9Ihq0eq73MDi
1ze6d2jIGce873qxn308BA2qhRPJNEbnPev5gI+5tU+UxebW8KLbk0EhoXB953Ix
3lgOIrT9Y6skRjsMSFmC6WN/O7ovu8QzGqxdywIDAQABAoIBAAaXoETtVT9GtpHW
qLaKHgYtLEO1tOFOhInWyolyZgL4inuRRva3CIvVEWK6TcnDyIlNL4MfcerehwGi
il4fQFvLR7E6UFcopvhJiSJHIcvPQ9FfNFR3dYcNOQ/IFvE73bEqMwSISPwiel6w
e1DjF3C7jHaS1s9PJfWFN982aublL/yLbJP+ou3ifdljS7QzjWZA8NRiMwmBGPIh
Yq8weR3jIVQl3ndEYxO7Cr/wXXebZwlP6CPZb67rBy0jg+366mxQbDZIwZYEaUME
zY5izFclr/kKj4s7NTRkC76Yx+rTNP5+BX+JT+rgz5aoQq8ghMw43NYwxjXym/MX
c8X8g0ECgYEA1crBUAR1gSkM+5mGjjoFLJKrFP+IhUHFh25qGI4Dcxxh1f3M53le
wF1rkp5SJnHRFm9IW3gM1JoF0PQxI5aXHRGHphwPeKnsQ/xQBRWCeYpqTme9amJV
tD3aDHkpIhYxkNxqol5gDCAt6tdFSxqPaNfdfsfaAOXiKGrQESUjIBcCgYEAxvmI
2ROJsBXaiM4Iyg9hUpjZIn8TW2UlH76pojFG6/KBd1NcnW3fu0ZUU790wAu7QbbU
i7pieeqCqSYcZsmkhnOvbdx54A6NNCR2btc+si6pDOe1jdsGdXISDRHFb9QxjZCj
6xzWMNvb5n1yUb9w9nfN1PZzATfUsOV+Fy8CbG0CgYEAifkTLwfhqZyLk2huTSWm
pzB0ltWfDpj22MNqVzR3h3d+sHLeJVjPzIe9396rF8KGdNsWsGlWpnJMZKDjgZsz
JQBmMc6UMYRARVP1dIKANN4eY0FSHfEebHcqXLho0mXOUTXe37DWfZza5V9Oify3
JquBd8uUptW1Ue41H4t/ErsCgYEArc5FYtF1QXIlfcDz3oUGz16itUZpgzlb71nd
1cbTm8EupCwWR5I1j+IEQU+JTUQyI1nwWcnKwZI+5kBbKNJUu/mLsRyY/UXYxEZh
ibrNklm94373kV1US/0DlZUDcQba7jz9Yp/C3dT/RlwoIw5mP3UxQCizFspNKOSe
euPeaxUCgYEAntklXwBbokgdDup/u/3ms5Lb/bm22zDOCg2HrlWQCqKEkWkAO6R5
/Wwyqhp/wTl8VXjxWo+W+DmewGdPHGQQ5fFdqgpuQpGUq24YZS8m66v5ANBwd76t
IZdtF5HXs2S5CADTwniUS5mX1HO9l5gUkk+h0cH5JnPtsMCnAUM+BRY=
-----END RSA PRIVATE KEY-----
```

Since we can't connect to Bandit 26 just yet, we go back to Bandit 25.  
By looking at the **/etc/passwd/** file, we see that the bandit 26 has **/usr/bin/showtext** set instead of the bash shell.  

**cat** /usr/bin/showtext  
```
#!/bin/sh

export TERM=linux

more ~/text.txt
exit 0
```

We can't read the text file but we know that **more** gets called.  
By minimizing the terminal when we try to log via SSH, it'll trigger the more command since the entire logo can't be displayed.  

Then, we can open **vim** by typing **v**. After that, we can enter the **:set shell=/bin/bash** command.  
Once we have our bash shell, we can use **:!cat /etc/bandit_pass/bandit26**.   
> 5czgV9L3Xx8JPOyRbXh6lQbmIOWvPT6Z  
##

### Bandit 26
###### Good job getting a shell! Now hurry and grab the password for bandit27!

We still can't log in to bandit 26.   
We go back and repeat the steps from the previous level.  

**:!ls -la** 
```
-rwsr-x---  1 bandit27 bandit26 7296 May  7  2020 bandit27-do
```

We do have reand and execute access to a binary called bandit27-do. We might assume what this is doing from previous experience.  

**:!/home/bandit26/bandit27-do** cat /etc/bandit_pass/bandit27
> 3ba3118a22e93127a4ed485be72ef5ea
##

### Bandit 27
###### There is a git repository at ssh://bandit27-git@localhost/home/bandit27-git/repo. Clone the repository and find the password for the next level.

Create a directory inside of /tmp because we don't have write access in our home directory.  

**git** clone ssh://bandit27-git@localhost/home/bandit27-git/repo  

> 0ef186ac70e04ea33b4c1853d2526fa2
##

### Bandit 28
###### There is a git repository at ssh://bandit29-git@localhost/home/bandit29-git/repo. Clone the repository and find the password for the next level.

**cat** README.md
```
## credentials

- username: bandit29
- password: xxxxxxxxxx
```

We can use the **git log** to see the logs of the git repository.  
We have one interesting log that caught my attention.  
```
commit edd935d60906b33f0619605abd1689808ccdd5ee
Author: Morla Porla <morla@overthewire.org>
Date:   Thu May 7 20:14:49 2020 +0200

    fix info leak
```

**git log -p** shows the logs with the differences made after each commit.
```
commit edd935d60906b33f0619605abd1689808ccdd5ee
Author: Morla Porla <morla@overthewire.org>
Date:   Thu May 7 20:14:49 2020 +0200

    fix info leak

diff --git a/README.md b/README.md
index 3f7cee8..5c6457b 100644
--- a/README.md
+++ b/README.md
@@ -4,5 +4,5 @@ Some notes for level29 of bandit.
 ## credentials
 
 - username: bandit29
-- password: bbc96594b4e001778eee9975372716b2
+- password: xxxxxxxxxx
```

> bbc96594b4e001778eee9975372716b2
##

### Bandit 29
###### There is a git repository at ssh://bandit29-git@localhost/home/bandit29-git/repo. Clone the repository and find the password for the next level.

Here, **git log -p** doesn't seem to offer us much information at first.  
We can check the current branch of the repository by using **git branch**.  

```
* master
```

Using **git branch -a** will show the other branches as well. 
We use **git checkout [branch]** to check the other brancahes.  

```
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev
  remotes/origin/master
  remotes/origin/sploits-dev
```

Finally, in /remotes/origin/dev, if we cheeck the logs we find the password.  

```
commit bc833286fca18a3948aec989f7025e23ffc16c07
Author: Morla Porla <morla@overthewire.org>
Date:   Thu May 7 20:14:52 2020 +0200

    add data needed for development

diff --git a/README.md b/README.md
index 1af21d3..39b87a8 100644
--- a/README.md
+++ b/README.md
@@ -4,5 +4,5 @@ Some notes for bandit30 of bandit.
 ## credentials
 
 - username: bandit30
-- password: <no passwords in production!>
+- password: 5b90576bedb2cc04c86a9e924ce42faf
```
> 5b90576bedb2cc04c86a9e924ce42faf
##

### Bandit 30
###### There is a git repository at ssh://bandit30-git@localhost/home/bandit30-git/repo. Clone the repository and find the password for the next level.

**cd** .git   
Finally, inside **packet-refs** we have an interesting file.
```
f17132340e8ee6c159e0a4a6bc6f80e1da3b1aea refs/tags/secret
```

**git** show f17132340e8ee6c159e0a4a6bc6f80e1da3b1aea

> 47e603bb428404d265f59c42920d81e5
