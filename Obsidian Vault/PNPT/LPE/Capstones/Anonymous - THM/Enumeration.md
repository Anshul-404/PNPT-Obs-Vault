# Port 139,445 - SMB
---
- Anonymous Access:
	- `smbclient -L \\\\10.10.230.109\\  -U anonymous`
	- ![[Pasted image 20250502155730.png]]
- Access the `pics`share and download the pics:
	- ![[Pasted image 20250502155805.png]]
- Nothing special, just pictures of dogs (nothing hidden)

# Port 22 - FTP
---
-  Anonymous Access:
	- `ftp anonymous@10.10.230.109`
	- ![[Pasted image 20250502160809.png]]
- `/scripts` **share is writable**
- `clean.sh` code:
	- ![[Pasted image 20250502161146.png]]