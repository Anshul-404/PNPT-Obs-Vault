# Enumeration as SkyFuck
---
- Found `gpg`credentials and `tryhackme.asc`
	- ![[Pasted image 20250503050406.png]]
- We need a password to decode the `credential.pgp`file:
	- `gpg -d credential.pgp`
	- ![[Pasted image 20250503050449.png]]
- Transfer `tryhackme.asc` file and crack the using `gpg2john`
	- `gpg2john tryhackme.asc > gpghash.txt`
	- `john gpghash.txt --wordlist=/usr/share/wordlists/rockyou.txt`
	- ![[Pasted image 20250503050525.png]]
	- Decrypt using `alexandru`:
		- ![[Pasted image 20250503050600.png]] - `merlin`:`asuyusdoiuqoilkda312j31k2j123j1g23g12k3g12kj3gk12jg3k12j3kj123j`
- Using Linpeas:
	- ![[Pasted image 20250502164942.png]]
	- **Writeable Path Found**
- Also found a crontab running as root:
	- ![[Pasted image 20250502165101.png]]

# Enumeration as  Merlin
---
- `su merlin`
- `sudo -l`
	- ![[Pasted image 20250503050758.png]]
- ![[Pasted image 20250503050818.png]]
- ![[Pasted image 20250503050847.png]]