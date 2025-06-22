# Gobuster
---
- We should use the following wordlists:
	- `/usr/share/wordlists/dirb/big.txt`
	- `/usr/share/seclists/Discovery/Web-Content/common.txt`
	- `/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`
	- `/usr/share/wordlists/dirb/common.txt`
- `gobuster dir -u http://10.10.10.121/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`