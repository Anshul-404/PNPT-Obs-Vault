# Port 80
---
![[Pasted image 20250502150341.png]]
- Gobuster scan:
	- `gobuster dir -u http://10.10.85.35/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt  -n -x zip,txt -t 100`
	- ![[Pasted image 20250502150407.png]]
- In `/content`, we found `changelog`:
	- ![[Pasted image 20250502151032.png]]
	- **SweetRice : 1.5.1**