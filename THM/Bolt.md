###### A hero is unleashed
Room: https://tryhackme.com/room/bolt
##

**sudo nmap** -sS -sV xx.xx.xx.xxx
```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
8000/tcp open  http    (PHP 7.2.32-1)
```

We can see that an Apache server is running on port 80.  
However, after a little bit of looking around, I found nothing of interest.   
On the other hand, the port 8000 seems to host a website running the Bolt CMS, which is a Content Management System written in PHP.   

From the Bolt's documentation we can see that the login portal is located at www.domain/bolt/login  
After navigating a bit around the website, we can see a page called "message for IT Department" which reveals the administrator password.  
   

**sudo gobuster** dir -u http://xx.xx.xx.xxx:8000/bolt -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt  

```
/about                (Status: 302) [Size: 288] [--> /bolt/login]
/login                (Status: 200) [Size: 6762]                 
/files                (Status: 302) [Size: 288] [--> /bolt/login]
/profile              (Status: 302) [Size: 288] [--> /bolt/login]
/users                (Status: 302) [Size: 288] [--> /bolt/login]
/upload               (Status: 302) [Size: 288] [--> /bolt/login]
/tr                   (Status: 302) [Size: 288] [--> /bolt/login]
/logout               (Status: 302) [Size: 288] [--> /bolt/login]
/changelog            (Status: 302) [Size: 288] [--> /bolt/login]
/extensions           (Status: 302) [Size: 288] [--> /bolt/login]
```

I managed to log in with bolt:boltadmin123 credentials.  

From the dashboard page we can see that the current version of Bolt running is 3.7.1  
A RCE exploit developed for the version 3.7.0 can be found on **exploit-db** and it seems to work on 3.7.1 as well after a little bit of tweaking.  

Using this exploit, we gen remote access to the target machine as root.  

The flag is located at **/home/flag.txt** and is **THM{wh0_d035nt_l0ve5_b0l7_r1gh7?}**  
