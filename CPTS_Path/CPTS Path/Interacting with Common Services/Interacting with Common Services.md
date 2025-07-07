# File Share Services
---
- A file sharing service is a type of service that provides, mediates, and monitors the transfer of computer files.

## Server Message Block (SMB)
- SMB is commonly used in Windows networks, and we will often find share folders in a Windows network. We can interact with SMB using the GUI, CLI, or tools. 

- **Windows**: 
	- On Windows GUI, we can press `[WINKEY] + [R]` to open the Run dialog box and type the file share location, e.g.: `\\192.168.220.129\Finance\`
		- ![[Pasted image 20250707052425.png]]
	- Suppose the shared folder **allows anonymous authentication**, or we **are authenticated with a user who has privilege over that shared folder.**
		- In that case, **we will not receive any form of authentication request, and it will display the content of the shared folder.**
	- If we do not have access, we will receive an authentication request.
		- ![[Pasted image 20250707052449.png]]
	
	- **Windows CMD - DIR**
		- `dir \\192.168.220.129\Finance\`
		  
	- The **command [net use](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/gg651155\(v=ws.11\)) connects a computer to or disconnects a computer from a shared resource** or displays information about computer connections: (map its content to the **drive letter `n`**.)
		- ` net use n: \\192.168.220.129\Finance`
	- We can also provide a username and password to authenticate to the share:
		- `net use n: \\192.168.220.129\Finance /user:plaintext Password123`
	- With the shared folder mapped as the `n` drive, we can execute Windows commands as if this shared folder is on our local computer.

	- **Finding Strings**:
		- `dir n:\*cred* /s /b`
		- `dir n:\*secret* /s /b`
	- If we want to search for a **specific word within a text file**, we can use [findstr](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/findstr).
		- `findstr /s /i cred n:\*.*`

	- **Windows PowerShell**:
		- `Get-ChildItem \\192.168.220.129\Finance\`
		- Instead of `net use`, we can use `New-PSDrive` in PowerShell.
		- `New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem"`
		- To provide a username and password with Powershell, we need to create a [PSCredential object](https://docs.microsoft.com/en-us/dotnet/api/system.management.automation.pscredential). It offers a centralized way to manage usernames, passwords, and credentials:
		```powershell
		PS C:\htb> $username = 'plaintext'
		PS C:\htb> $password = 'Password123'
		PS C:\htb> $secpassword = ConvertTo-SecureString $password -AsPlainText -Force
		PS C:\htb> $cred = New-Object System.Management.Automation.PSCredential $username, $secpassword
		PS C:\htb> New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem" -Credential $cred
		```
		- In PowerShell, we can use the command `Get-ChildItem` or the short variant `gci` instead of the command `dir`:
			- `N:`
			- `(Get-ChildItem -File -Recurse | Measure-Object).Count`
		- We can use the property `-Include` to find specific items from the directory specified by the Path parameter:
			- `Get-ChildItem -Recurse -Path N:\ -Include *cred* -File`
		- The **`Select-String` cmdlet uses regular expression matching** to search for text patterns in input strings and files. **We can use `Select-String` similar to `grep`**:
			- `Get-ChildItem -Recurse -Path N:\ | Select-String "cred" -List`
## Linux 
---
- **Linux - Mount**:
	- `sudo mkdir /mnt/Finance`
	- `sudo mount -t cifs -o username=plaintext,password=Password123,domain=. //192.168.220.129/Finance /mnt/Finance`
	- As an alternative, we can use a credential file.:
		- `mount -t cifs //192.168.220.129/Finance /mnt/Finance -o credentials=/path/credentialfile`
	- The file `credentialfile` has to be structured like this:
		- ![[Pasted image 20250707053207.png]]
	- Let's hunt for a filename that contains the string `cred`:

- **Linux - Find**:
	- `find /mnt/Finance/ -name *cred*`
	- Next, let's find files that contain the string `cred`:
		- `grep -rn /mnt/Finance/ -ie cred`

# Other Services
---
- There are other file-sharing services such as FTP, TFTP, and NFS that we can attach (mount) using different tools and commands.
- As we discover new file-sharing services, we will need to investigate how they work and what tools we can use to interact with them.

## Email
- The Simple Mail Transfer Protocol (SMTP) is an email delivery protocol used to send mail over the internet. 
- Likewise, a supporting protocol must be used to retrieve an email from a service. There are two main protocols we can use POP3 and IMAP.
- [[SMTP]], [[IMAP - POP3]]
- We can use a **mail client such as [Evolution](https://wiki.gnome.org/Apps/Evolution),** the official personal information manager, and mail client for the GNOME Desktop Environment.

- Linux - Install Evolution:
	- `sudo apt-get install evolution`
	- ![[Pasted image 20250707054008.png]]
	- https://www.youtube.com/watch?v=xelO2CiaSVs
- We can use the domain name or IP address of the mail server. 
- If the server uses **SMTPS or IMAPS, we'll need the appropriate encryption** method (TLS on a dedicated port or STARTTLS after connecting).


# Databases
---
- We have three common ways to interact with databases:
	1. Command Line Utilities (mysql or sqsh)
	2. Programming Languages
	3. A GUI application to interact with databases such as HeidiSQL, MySQL Workbench, or SQL Server Management Studio.

## Command Line Utilities
- **MSSQL**:
	- To interact with [MSSQL (Microsoft SQL Server)](https://www.microsoft.com/en-us/sql-server/sql-server-downloads) with Linux we can use [sqsh](https://en.wikipedia.org/wiki/Sqsh) or [sqlcmd](https://docs.microsoft.com/en-us/sql/tools/sqlcmd-utility) if you are using Windows.
	- `Sqsh` is much more than a friendly prompt. We can start an interactive SQL session as follows:
	- Linux - sqsh:
		- `sqsh -S 10.129.20.13 -U username -P Password123`

	- The `sqlcmd` utility **lets you enter Transact-SQL statements, system procedures, and script files** through a variety of available modes:
		- ![[Pasted image 20250707054606.png]]
	- Windows - SQLCMD:
		- `sqlcmd -S 10.129.20.13 -U username -P Password123`
## MySQL:
- To interact with [MySQL](https://en.wikipedia.org/wiki/MySQL), we can use MySQL binaries for Linux (`mysql`) or Windows (`mysql.exe`).
- Linux - MySQL:
	- `mysql -u username -pPassword123 -h 10.129.20.13`
- Windows - MySQL:
	- `mysql.exe -u username -pPassword123 -h 10.129.20.13`

# GUI Application
---
- Database engines commonly have their own GUI application. 
- MySQL has [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) and MSSQL has [SQL Server Management Studio or SSMS](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms), we can install those tools in our attack host and connect to the database.
- SMS is only supported in Windows. An alternative is to use community tools such as [dbeaver](https://github.com/dbeaver/dbeaver).
- [dbeaver](https://github.com/dbeaver/dbeaver) is a multi-platform database tool for Linux, macOS, and Windows that supports connecting to multiple database engines such as MSSQL, MySQL, PostgreSQL, among others.

- Install dbeaver:
	- `sudo dpkg -i dbeaver-<version>.deb`
- Run dbeaver:
	- `dbeaver &`
- Video - Connecting to MSSQL DB using dbeaver:
	- https://www.youtube.com/watch?v=gU6iQP5rFMw => MSSQL
	- https://www.youtube.com/watch?v=PeuWmz8S6G8 => MySQL

# Tools
---
![[Pasted image 20250707054930.png]]
# General Troubleshooting
---
![[Pasted image 20250707054957.png]]