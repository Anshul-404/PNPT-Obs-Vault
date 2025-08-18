# Enumeration
---
## FTP - 21
- Anonymous Access Allowed
- ![[Pasted image 20250815021157.png]]
- We have no access here

## NFS - 2049
- `showmount -e 10.10.10.180`
- ![[Pasted image 20250815022401.png]]
- Mount using:
	- `mount -t nfs 10.10.10.180:/site_backups /mnt/site_backups`
- Enumerating to find:
	- ![[Pasted image 20250815044624.png]]
	- ![[Pasted image 20250815044652.png]]
	- Umbraco.sdf is a file that contains database
	- Crack the Password:
		- ![[Pasted image 20250815044727.png]]
## HTTP - 80
---
- We have `umbraco` directory which I found from the files from the mount
- Login in this portal