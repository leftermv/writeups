###### SSH Credentials
> User: leviathan0  
> Password: leviathan0  
> Address: leviathan.labs.overthewire.org   
> Port: 2223  

### Leviathan 0
###### There is no information for this level, intentionally.  

**cat** .backup/bookmarks.hmtl | **grep** levi
```
This will be fixed later, the password for leviathan1 is rioGegei8m" ADD_DATE="1155384634" 
LAST_CHARSET="ISO-8859-1" ID="rdf:#$2wIU71">password to leviathan1</A>
```
 > rioGegei8m
##

### Leviathan 1
###### There is no information for this level, intentionally.

It seems that the **check** binary requires a password to run.  
Once we look at the source code, we can somehow guess some things.  
* �[�password: /bin/shWrong password, Good Bye ..   

The live above could indicate that if the password is right, a shell is spawned. Otherwise, a message is shown to the user.  
However, by looking more careful we can see some words hidden in the unknown characters.  
* �sex
* �secrf
* �god
* �love�

The spaces in between could be the words the, making the sentence **Sex is the secret the god love**.   
However, I don't have any clue if that is the right sentence.  

Using **sex** as a password on its own seems to let us run the script and spawn a shell.  
**Note**: Since Python is installed on the machine, we can use ```python3 -c 'import pty;pty.spawn("/bin/bash")'``` to spawn a bash shell.

**whoami** returns us the leviathan2 user, meaning that we have access to read from /etc/leviathan_pass/leviathan2 file.
> ougahZi8Ta
##

### Leviathan 2
###### There is no information for this level, intentionally.
T.B.E
##

### Leviathan 3
###### There is no information for this level, intentionally.
T.B.E
##

### Leviathan 4
###### There is no information for this level, intentionally.
T.B.E
##

### Leviathan 5
###### There is no information for this level, intentionally.
T.B.E
##

### Leviathan 6
###### There is no information for this level, intentionally.
T.B.E
##
