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
