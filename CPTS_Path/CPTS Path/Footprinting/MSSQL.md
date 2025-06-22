- Microsoft SQL (MSSQL) is Microsoft's SQL-based relational database management system. 
- Pentesters may find **==Impacket's mssqlclient.py==** to be the most useful due to SecureAuthCorp's Impacket project being present on many pentesting distributions at install.

# MSSQL Databases
---
![[Pasted image 20250617060614.png]]

# Default Configuration
---
- When an admin initially installs and configures MSSQL to be network accessible, ==**the SQL service will likely run as NT SERVICE\MSSQLSERVER.**==
- Authentication being set to **Windows Authentication** means that the underlying Windows OS will process the login request and **use either the local SAM database or the domain controller** (hosting Active Directory) 

# Dangerous Settings
---
- MSSQL clients not using encryption to connect to the MSSQL server
- The use of self-signed certificates when encryption is being used. It is possible to spoof self-signed certificates
- The use of [named pipes](https://docs.microsoft.com/en-us/sql/tools/configuration-manager/named-pipes-properties?view=sql-server-ver15)
- Weak & default `sa` credentials. Admins may forget to disable this account

# Footprinting
---
- `nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.201.248`
- `msf6 auxiliary(scanner/mssql/mssql_ping)` => scan the MSSQL service and provide helpful information

# Connecting
---
- `python3 mssqlclient.py Administrator@10.129.201.248 -windows-auth`
- `impacket-mssqlclient Administrator@10.129.201.248 -windows-auth`