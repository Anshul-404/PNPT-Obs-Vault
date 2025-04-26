- Now that we have NT hash of `svc_backup`, we can use `evil_winrm`to login
	- ![[Pasted image 20250426044931.png]]

# Enumeration
---
- Use bloodhound and load it. Mark `svc_backup`as `owned`
- `Shortest Path To High Value Targets`
	- `svc_backup`is member of `BACKUP OPERATORS`groups:
	- ![[Pasted image 20250426045431.png]]
	- **==AD group Backup Operators have privileges to read files, traverse directories, and backup everything. This includes files and folders on DCs.==**