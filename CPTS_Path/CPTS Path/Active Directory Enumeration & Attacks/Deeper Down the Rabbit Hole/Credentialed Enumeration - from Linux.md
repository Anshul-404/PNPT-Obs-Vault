- Now that we have acquired a foothold in the domain, it is time to **dig deeper using our low privilege domain user credentials.**
- We are interested in information about **==domain user and computer attributes, group membership, Group Policy Objects, permissions, ACLs, trusts, and more.==**

# CrackMapExec
---
- [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) (CME, now NetExec) is a powerful toolset to help with assessing AD environments.
- We can see that **we can use the tool with MSSQL, SMB, SSH, and WinRM credentials.** Let's look at our options for CME with the **SMB protocol:**
	- `crackmapexec smb -h`
- CME **offers a help menu for each protocol** (i.e., `crackmapexec winrm -h`, etc.). 
- For now, the flags we are interested in are:
	- `-u Username`The user whose credentials we will use to authenticate`
	- `-p Password`User's password`
	- `Target (IP or FQDN)`Target host to enumerate` (in our case, the Domain Controller)`
	- `--users`Specifies to enumerate Domain Users`
	- `--groups`Specifies to enumerate domain groups`
	- `--loggedon-users`Attempts to enumerate what users are logged on to a target, if any

## ==CME - Domain User Enumeration==
- We start by **pointing CME at the Domain Controller and using the credentials** for the `forend` user to **retrieve a list of all domain users.**
	- `sudo crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 --users`
	- ![[Pasted image 20250807070611.png]]

## CME - Domain Group Enumeration
- `sudo crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 --groups`
- ![[Pasted image 20250807070636.png]]
- The output also shows the **built-in groups on the Domain Controller, such as `Backup Operators`.**
- Take note of **==key groups like `Administrators`, `Domain Admins`, `Executives`, any groups that may contain privileged IT admins, etc.==**
- These groups will likely **contain users with elevated privileges worth targeting during our assessment.**

## CME - Logged On Users
- `sudo crackmapexec smb 172.16.5.130 -u forend -p Klmcargo2 --loggedon-users`
- ![[Pasted image 20250807070736.png]]

## ==CME Share Searching==
- We can **use the `--shares` flag to enumerate available shares on the remote host** and the level of access our user account has to each share (READ or WRITE access).
- Let's run this against the INLANEFREIGHT.LOCAL Domain Controller:
- `sudo crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 --shares`
	- ![[Pasted image 20250807070902.png]]
- The `Department Shares`, `User Shares`, and `ZZZ_archive` shares would be worth digging into further as they may contain sensitive data such as passwords or PII.

## ==Spider_Plus==
---
- Next, **we can dig into the shares and ==spider each directory looking for files**.== 
- The module **==`spider_plus` will dig through each readable share on the host and list all readable files==**:
	- `sudo crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 -M spider_plus --share 'Department Shares'`
	- ![[Pasted image 20250807071013.png]]
	- When completed, CME **writes the results to a JSON file located at `/tmp/cme_spider_plus/<ip of host>`**
	- We could dig around for **interesting files such as `web.config` files or scripts that may contain passwords**
	- ` head -n 10 /tmp/cme_spider_plus/172.16.5.5.json `

# SMBMap
---
- SMBMap is great for **enumerating SMB shares from a Linux attack host**. It can be used to gather a **listing of shares, permissions, and share contents if accessible.** 
- Once access is obtained, **it can be used to download and upload files** and **execute remote commands.**
- As with other tools, we can type the command `smbmap` `-h` to view the tool usage menu
- Aside from listing shares, we can **use SMBMap to recursively list directories, list the contents of a directory, search file contents**, and more
## SMBMap To Check Access
- `smbmap -u forend -p Klmcargo2 -d INLANEFREIGHT.LOCAL -H 172.16.5.5`
	- ![[Pasted image 20250807072207.png]]
- The above will tell us what our user can access and their permission levels

## Recursive List Of All Directories
- `smbmap -u forend -p Klmcargo2 -d INLANEFREIGHT.LOCAL -H 172.16.5.5 -R 'Department Shares' --dir-only`
- ![[Pasted image 20250807072301.png]]
- The use of **`--dir-only` provided only the output of all directories and did not list all files.**

# rpcclient
---
- [rpcclient](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html) is a handy tool created for use with the **Samba protocol and to provide extra functionality via MS-RPC.**
- It can enumerate, add, change, and even remove objects from AD. It is highly versatile; we just have to find the correct command to issue for what we want to accomplish.
- The **man page for rpcclient is very helpful for this; just type `man rpcclient`** into your attack host's shell and review the options available.
- Due to **SMB NULL sessions on some of our hosts,** we can perform **authenticated or unauthenticated enumeration using rpcclient in the INLANEFREIGHT.LOCAL domain.**

- An example of **using rpcclient from an unauthenticated standpoint** (if this configuration exists in our target domain) would be:
	- `rpcclient -U "" -N 172.16.5.5`

## rpcclient Enumeration
- While looking at users in rpcclient, you may notice a field called `rid:` beside each user
- A [Relative Identifier (RID)](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/security-identifiers) is a unique identifier (represented in hexadecimal format) utilized by Windows to track and identify objects.
	- ![[Pasted image 20250807072830.png]]
- Accounts **like the built-in Administrator for a domain will have a RID [administrator] rid:[0x1f4], which, when converted to a decimal value, equals `500`.**
- The built-in Administrator account **will always have the RID value `Hex 0x1f4`, or 500.**
- Since this **==value is unique to an object, we can use it to enumerate further information about it from the domain.==**

## RPCClient User Enumeration By RID
- `queryuser 0x457`
- ![[Pasted image 20250807072933.png]]
- When we searched for information using the `queryuser` command against the RID `0x457`, RPC returned the user information for `htb-student` as expected.
- If we wished to **enumerate all users to gather the RIDs** for more than just one, **we would use the `enumdomusers` command.**

## Enumdomusers
- `enumdomusers`
- ![[Pasted image 20250807073018.png]]
- Using it in this manner will print out all domain users by name and RID.
# Impacket Toolkit
---
- Impacket is a versatile toolkit that provides us with **many different ways to enumerate, interact, and exploit Windows protocol**s and find the information we need using Python.

## Psexec.py
- One of the **most useful tools in the Impacket suite is `psexec.py`.**
- Psexec.py is a **clone of the Sysinternals psexec executable,** but works slightly differently from the original
- The tool **creates a remote service by uploading a randomly-named executable to the `ADMIN$` share on the target host**
- **It then registers the service via `RPC` and the `Windows Service Control Manager`.** 
- Once established, **communication happens over a named pipe, providing an interactive remote shell as `SYSTEM` on the victim host**

Using psexec.py:
- `psexec.py inlanefreight.local/wley:'transporter@4'@172.16.5.125  `
## wmiexec.py
- Wmiexec.py utilizes a **semi-interactive shell** where commands are executed through [Windows Management Instrumentation](https://docs.microsoft.com/en-us/windows/win32/wmisdk/wmi-start-page).
- It **does not drop any files or executables on the target host** and generates fewer logs than other modules.
- After connecting, **it runs as the local admin user we connected with** (this can be less obvious to someone hunting for an intrusion than seeing SYSTEM executing many commands)
- This is a ==**more stealthy approach== to execution on hosts than other tools**, **but would still likely be caught by most modern anti-virus** and EDR systems.

Using wmiexec.py
- `wmiexec.py inlanefreight.local/wley:'transporter@4'@172.16.5.5  `
- Note that **this shell environment is not fully interactive**, so each **command issued will execute a new cmd.exe** from WMI and execute your command.
- The downside of this is that if a vigilant defender checks event logs and looks at event ID [4688: A new process has been created](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4688), they will see a new process created to spawn cmd.exe and issue a command

# Windapsearch
---
- **[Windapsearch](https://github.com/ropnop/windapsearch) is another handy Python script we can use to enumerate users, groups, and computers** from a Windows domain by **utilizing LDAP queries**.
- **The `--da` (enumerate domain admins group members ) option and the `-PU` ( find privileged users) options**. **==The `-PU` option is interesting because it will perform a recursive search for users with nested group membership.==**

## Windapsearch - Domain Admins
- `python3 windapsearch.py --dc-ip 172.16.5.5 -u forend@inlanefreight.local -p Klmcargo2 --da`
- ![[Pasted image 20250807073552.png]]
- To identify more potential users, **we can run the tool with the `-PU` flag and check for users with elevated privileges** that may have gone unnoticed.

## ==Windapsearch - Privileged Users==
- `python3 windapsearch.py --dc-ip 172.16.5.5 -u forend@inlanefreight.local -p Klmcargo2 -PU`
- ![[Pasted image 20250807073636.png]]

# Bloodhound.py
---
- REFER TO **==Domain Enumeration Bloodhound in PNPT Preparation Notes==**
  
- Once we have domain credentials, we can run the [BloodHound.py](https://github.com/fox-it/BloodHound.py)BloodHound ingestor from our Linux attack host.
- BloodHound is one of, **if not the most impactful tools ever released for auditing Active Directory security**, and it is hugely beneficial for us as penetration testers
- The tool consists of two parts: 
	- **The [SharpHound collector](https://github.com/BloodHoundAD/BloodHound/tree/master/Collectors) written in C# for use on Windows systems**, or for this section, the BloodHound.py collector (also referred to as an `ingestor`) 
	- **and the [BloodHound](https://github.com/BloodHoundAD/BloodHound/releases) GUI tool which allows us to upload collected** **data** in the form of JSON files.
- Once uploaded, we can run various pre-built queries or write custom queries using [Cypher language](https://blog.cptjesus.com/posts/introtocypher).

- Executing BloodHound.py:
	- `sudo bloodhound-python -u 'forend' -p 'Klmcargo2' -ns 172.16.5.5 -d inlanefreight.local -c all `
	- We **specified our nameserver as the Domain Controller with the `-ns` flag and the domain, INLANEFREIGHt.LOCAL with the `-d` flag**. 
	- **The `-c all` flag told the tool to run all checks.** Once the script finishes, **we will see the output files in the current working directory in the format of <date_object.json>.**
![[Pasted image 20250807073930.png]]

# LABS
---
=>  What AD User has a RID equal to Decimal 1170? 
- `rpcclient -U "" -N 172.16.5.5`
- Decimal to hex => 1170 -> 0x492
- `queryuser 0x492`
	- ![[Pasted image 20250807074609.png]]

=> What is the membercount: of the "Interns" group? 
- `sudo crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 --groups | grep -i interns`
- ![[Pasted image 20250807074835.png]]