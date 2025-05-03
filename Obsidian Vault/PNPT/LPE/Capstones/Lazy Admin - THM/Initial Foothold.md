# Enumeration
---
- Searching the SweetRice 1.5.1 version, we find **backup disclosure**:
	- ![[Pasted image 20250502151141.png]]
	- Path: `/content/inc/mysql_backup`
	- ![[Pasted image 20250502151331.png]]
- ![[Pasted image 20250502151529.png]]
- `manager`: `42f749ade7f9e195bf475f37a44cafcb`: `Password123`
- Crack Using hashcat:
	- `hashcat hash /usr/share/wordlists/rockyou.txt -m 0`=> MD5
	- ![[Pasted image 20250502151651.png]]
-  Gobuster in `/content`folder reveals `/as`and `attachment` directory, which has login page:
	- ![[Pasted image 20250502152456.png]]
	- ![[Pasted image 20250502152100.png]]
- ![[Pasted image 20250502152133.png]]


# File Upload using `/media_center`:
---
- Automation script (took reference from): https://www.exploit-db.com/exploits/40716
![[Pasted image 20250502152228.png]]
- **Upload a php5 reverse shell**:
- ![[Pasted image 20250502152357.png]]
- Access the uploaded files from `/attachment`directory
	- ![[Pasted image 20250502152650.png]]
- ![[Pasted image 20250502152712.png]]


# Enumeration as `www-data`
---
- Stabilize shell:
	- ![[Pasted image 20250502153316.png]]
- `mysql_login.txt`credentials found in `/home/itguy`:
	- ![[Pasted image 20250502153400.png]]
	- ![[Pasted image 20250502153415.png]]
- We can also run `sudo -l`:
	- ![[Pasted image 20250502153705.png]]
