- **Once we gain a foothold in the domain, our goal shifts** to advancing our position further by **moving laterally or vertically to obtain access to other hosts**, and eventually achieve domain compromise or some other goal, depending on the aim of the assessment.
- Typically, if we take over an account with local admin rights over a host, or set of hosts, **we can perform a `Pass-the-Hash` attack to authenticate via the SMB protocol.**

- There are several other **ways we can move around a Windows domain**:
	- **`Remote Desktop Protocol` (`RDP`) - is a remote access/management protocol that gives us GUI** access to a target host
	- **[PowerShell Remoting](https://docs.microsoft.com/en-us/powershell/scripting/learn/ps101/08-powershell-remoting?view=powershell-7.2) - also referred to as PSRemoting or Windows Remote Management (WinRM)** access, is a r**emote access protocol that allows us to run commands or enter an interactive command-line** session on a remote host using PowerShell
	- **`MSSQL Server`** - an account with sysadmin privileges on an SQL **Server instance can log into the instance remotely and execute queries against the database**. This access can be used to run operating system commands in the context of the SQL Server service account through various methods

- We can enumerate this access in various ways. **==The easiest, once again, is via BloodHound,==** as the following edges exist to **show us what types of remote access privileges** a given user has:
	- [CanRDP](https://bloodhound.readthedocs.io/en/latest/data-analysis/edges.html#canrdp)
	- [CanPSRemote](https://bloodhound.readthedocs.io/en/latest/data-analysis/edges.html#canpsremote)
	- [SQLAdmin](https://bloodhound.readthedocs.io/en/latest/data-analysis/edges.html#sqladmin)

# Remote Desktop
---
- Typically, if we have control of a local admin user on a given machine, we will be able to access it via RDP.
- Sometimes, **we will obtain a foothold with a user that does not have local admin rights anywhere, but does have the rights to RDP into one or more machines.**
- Using **==PowerView, we could use the [Get-NetLocalGroupMember](https://powersploit.readthedocs.io/en/latest/Recon/Get-NetLocalGroupMember/) function to begin enumerating members of the `Remote Desktop Users` group on a given host==**. 

## Enumerating the Remote Desktop Users Group
- `Get-NetLocalGroupMember -ComputerName ACADEMY-EA-MS01 -GroupName "Remote Desktop Users"`
- ![[Pasted image 20250812014818.png]]
- We can see that **all Domain Users (meaning `all` users in the domain) can RDP to this host.**

## Checking the Domain Users Group's Local Admin & Execution Rights using BloodHound
-  Does the Domain Users group have local admin rights or execution rights (such as RDP or WinRM) over one or more hosts?
- ![[Pasted image 20250812015055.png]]
- If we gain control over a user through an attack such as LLMNR/NBT-NS Response Spoofing or Kerberoasting, **==we can search for the username in BloodHound to check what type of remote access rights they have either directly or inherited via group membership under `Execution Rights` on the `Node Info` tab.==**

## Checking Remote Access Rights using BloodHound
![[Pasted image 20250812015153.png]]
- We could also check the **`Analysis` tab and run the pre-built queries** `Find Workstations where Domain Users can RDP` or `Find Servers where Domain Users can RDP`.

# WinRM
---
- Like RDP, we may find that either a **specific user or an entire group has WinRM access** to one or more hosts.
- We can again use the **PowerView function `Get-NetLocalGroupMember` to the `Remote Management Users` group**. 
- This group has existed since the days of Windows 8/Windows Server 2012 to enable WinRM access without granting local admin rights.
  
	- `Get-NetLocalGroupMember -ComputerName ACADEMY-EA-MS01 -GroupName "Remote Desktop Users"`
	- ![[Pasted image 20250812014818.png]]
- We can also utilize this custom `Cypher query` in BloodHound to hunt for users with this type of access.
	- `MATCH p1=shortestPath((u1:User)-[r1:MemberOf*1..]->(g1:Group)) MATCH p2=(u1)-[:CanPSRemote*1..]->(c:Computer) RETURN p2`
	- ![[Pasted image 20250812015755.png]]
	- We could also add this as a **custom query to our BloodHound installation, so it's always available to us.**

## Establishing WinRM Session from Windows
- We can **use the [Enter-PSSession](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/enter-pssession?view=powershell-7.2) cmdlet** using PowerShell from a Windows host.
	- `$password = ConvertTo-SecureString "Klmcargo2" -AsPlainText -Force`
	- `$cred = new-object System.Management.Automation.PSCredential ("INLANEFREIGHT\forend", $password)`
	- `Enter-PSSession -ComputerName ACADEMY-EA-MS01 -Credential $cred`
- From our Linux attack host, we can use the tool [evil-winrm](https://github.com/Hackplayers/evil-winrm) to connect.

## Evil-Winrm
- `evil-winrm -i 10.129.201.234 -u forend`

# SQL Server Admin
---
- More often than not, **we will encounter SQL servers in the environments we face.**
- It is common to find user and service **accounts set up with sysadmin privileges on a given SQL server instance**. 
- We may obtain **credentials for an account with this access via Kerberoasting** (common) or **others such as LLMNR/NBT-NS Response Spoofing or password spraying.**
- Another way that you may find **SQL server credentials is using the tool [Snaffler](https://github.com/SnaffCon/Snaffler) to find web.config or other types of configuration** files that contain SQL server connection strings.

## Using a Custom Cypher Query to Check for SQL Admin Rights in BloodHound
- We can **check for `SQL Admin Rights` in the `Node Info` tab for a given user** or use this custom Cypher query to search:
	- `MATCH p1=shortestPath((u1:User)-[r1:MemberOf*1..]->(g1:Group)) MATCH p2=(u1)-[:SQLAdmin*1..]->(c:Computer) RETURN p2`
	- ![[Pasted image 20250812020044.png]]
- We can **use our ACL rights to authenticate with the `wley` user**, **change the password for the `damundsen` user** and then **authenticate with the target using a tool such as `PowerUpSQL`,** which has a handy [command cheat sheet](https://github.com/NetSPI/PowerUpSQL/wiki/PowerUpSQL-Cheat-Sheet).
- Let's assume we **changed the account password to `SQL1234!` using our ACL rights.**
We can now authenticate and run operating system commands.

## Enumerating MSSQL Instances with PowerUpSQL
- `cd .\PowerUpSQL\`
- `Import-Module .\PowerUpSQL.ps1`
- `Get-SQLInstanceDomain`
- ![[Pasted image 20250812020211.png]]
- We could then authenticate against the remote SQL server host and run custom queries or operating system commands.
	- `Get-SQLQuery -Verbose -Instance "172.16.5.150,1433" -username "inlanefreight\damundsen" -password "SQL1234!" -query 'Select @@version'`

## Running mssqlclient.py Against the Target
- `mssqlclient.py INLANEFREIGHT/DAMUNDSEN@172.16.5.150 -windows-auth`
## Viewing our Options with Access to the SQL Server
- `help`
- We could then **==choose `enable_xp_cmdshell` to enable the [xp_cmdshell stored procedure](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql?view=sql-server-ver15)==** which allows for one to **execute operating system commands via the database** if the account in question has the proper access rights.

## Choosing enable_xp_cmdshell
- `enable_xp_cmdshell`
- Finally, we can run commands in the format `xp_cmdshell <command>`.