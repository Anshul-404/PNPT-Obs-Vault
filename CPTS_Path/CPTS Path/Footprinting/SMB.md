- Server Message Block (SMB) is a client-server protocol that regulates access to files and entire directories and other network resources such as printers, routers, or interfaces released for the network.
- An SMB server can provide arbitrary parts of its local file system as shares. Therefore the hierarchy visible to a client is partially independent of the structure on the server.
- Access rights are defined by **Access Control Lists (ACL)**. They can be controlled in a fine-grained manner based on attributes such as execute, read, and full access for individual users or user groups. 

# SMB Versions
---
![[Pasted image 20250615075744.png]]

# Configuration
---
- `cat /etc/samba/smb.conf | grep -v "#\|\;" `
- ![[Pasted image 20250615075859.png]]

# Dangerous Settings
---
![[Pasted image 20250615075951.png]]

# Common Stuff
---
- Cat: `!cat prep-prod.txt`
- From the administrative point of view, we can check these connections using `smbstatus`. 
- Domain controller provides the workgroup with a definitive password server.
- he domain controllers keep track of users and passwords in their **own NTDS.dit and Security Authentication Module (SAM)** and authenticate each user when they log in for the first time and wish to access another machine's share.


# Footprinting
---
- `sudo nmap 10.129.14.128 -sV -sC -p139,445`

# RPCclient
---
- The rpcclient offers us many different requests with which we can execute specific functions on the SMB server to get information. 
	- `rpcclient -U "" 10.129.14.128`
	- ![[Pasted image 20250615080304.png]]

# Bruteforcing RIDs
---
![[Pasted image 20250615080334.png]]- `impacket-samrdump 10.129.14.128`

# Other Tools
---
- SMBmap: `smbmap -H 10.129.14.128`
- CrackMapExec: `crackmapexec smb 10.129.14.128 --shares -u '' -p ''`
- Enum4Linux: `./enum4linux-ng.py 10.129.14.128 -A`
- 