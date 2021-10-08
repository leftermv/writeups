
Room: https://tryhackme.com/room/rrootme

From the nmap scan, we find that only 2 ports are open.  

* Port 22 is the SSH Service running.
* Port 80 is a webserver, running on Apache 2.4.29.

Running a directory bruteforcing with gobuster, we find out a **/panel/** and a **/uploads** directory  

On that panel, we have the possibility to upload a desired file from our local machine.

I'm trying to upload [**pentestmonkey's PHP reverse shell**](https://github.com/pentestmonkey/php-reverse-shell) but we get an error that says that .php format is not accepted.  

We can bypass this using a .phtml extension when uploading the file.  

Start a netcat listener and navigate to **/uploads** directory, from where we access our uploaded file.

In order to spawn a bash shell, I'm using the **python3 -c 'import pty; pty.spawn("/bin/bash")'** command.

By navigating to www-data's user home directory, we find the **user.txt** file which contains the first flag. 

> THM{y0u_g0t_a_sh3ll} **[1]**

With no more clues at hand, I started to verify check if any files has any SUID permission set.  

> find / -perm /4000

After a long list of results, the **/usr/bin/python** is what I was needed.

According to [**GTFObins**](https://gtfobins.github.io/gtfobins/python/#suid), **/usr/bin/python -c 'import os; os.execl("/bin/sh", "sh", "-p")'** should let us escalate our privileges by abusing that SUID permission we have.  

Checking **whoami**, we see that the system is rooted, since we're under the **root** user account.  

The second and final flag is located at /root/root.txt and it is **THM{pr1v1l3g3_3sc4l4t10n}**.**[2]**
