# Port 445 / 139 : SMB
---
- anonymous login gave the following shares:
	- ![[Pasted image 20250421154139.png]]
- Access using:
	- `smbclient \\\\10.10.10.125\\Reports -U anonymous`
	- ![[Pasted image 20250421154201.png]]
- This **file is empty**, but looking at exif data using `exiftool`:
	- ![[Pasted image 20250421155510.png]]
	- username : `Luis`