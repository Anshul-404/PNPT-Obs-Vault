We can see that xlsm is a`zip`:
	![[Pasted image 20250422154519.png]]
So we can try to unzip it:
![[Pasted image 20250422154544.png]]

# Enumeration
---
- We can search through these files one by one, but I found a `vbaProject.bin`interesting file.
- Looking through this, we can see **username and password**
	- ![[Pasted image 20250422154656.png]]
	- `reporting`: `PcwTWTHRwryjc$c6`

# Using `impacket-mssqlclient`:
---
- We can **login using `impacket-mssqlclient`** with these credentials:
	- `impacket-mssqlclient QUERIER/reporting:'PcwTWTHRwryjc$c6'@10.10.10.125 -windows-auth`
		- ![[Pasted image 20250422154917.png]]
	- We can **enumerate directories** users using (though we don't have `xp_cmdshell`permission):
		- `EXEC xp_dirtree 'C:\Users', 1, 1;`
		- ![[Pasted image 20250422155115.png]]
	- `mssql-svc`**service user** which is **running** **mssql server**
