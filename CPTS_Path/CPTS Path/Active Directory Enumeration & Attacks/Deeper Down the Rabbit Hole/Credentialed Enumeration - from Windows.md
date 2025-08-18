# TTPs
---
## ActiveDirectory PowerShell Module
- The ActiveDirectory PowerShell module is a **group of PowerShell cmdlets for administering an Active Directory environment** from the command line.
- The **[Get-Module](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/get-module?view=powershell-7.2) cmdlet,** which is part of the [Microsoft.PowerShell.Core module](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/?view=powershell-7.2), **will list all available modules, their version, and potential commands for use.**
- This is a great way to see if **anything like Git or custom administrator scripts are installed.**
- f the module is not loaded, **run `Import-Module ActiveDirectory` to load it for use.**

- Discover Modules:
	- `Get-Module`
	- ![[Pasted image 20250808060249.png]]
- Load ActiveDirectory Module:
	- `Import-Module ActiveDirectory`
	- `Get-Module`
	- ![[Pasted image 20250808060309.png]]
- Get Domain Info:
	- `Get-ADDomain`
	- ![[Pasted image 20250808060333.png]]
- **==Get-ADUser==**:
	- We will be **filtering for accounts** **with the `ServicePrincipalName` property populated.** 
	- This will get us a listing of accounts that may be susceptible to a **Kerberoasting attack,** which we will cover in-depth after the next section.
	- `Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName`
	- ![[Pasted image 20250808060430.png]]
- Checking For Trust Relationships:
	- `Get-ADTrust -Filter *`

- Group Enumeration:
	- `Get-ADGroup -Filter * | select name`
	- ![[Pasted image 20250808060514.png]]

- Detailed Group Info:
	- `Get-ADGroup -Identity "Backup Operators"`
	- ![[Pasted image 20250808060540.png]]

- **==Group Membership:==**
	- `Get-ADGroupMember -Identity "Backup Operators"`
	- ![[Pasted image 20250808060613.png]]
	- We can see that one account, **`backupagent`, belongs to this group.** 
	- It is worth noting this down because **==if we can take over this service account through some attack, we could use its membership in the Backup Operators group to take over the domain.==**

# PowerView
---
- [PowerView](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon) is a tool written in PowerShell to help us **gain situational awareness within an AD environment.**
- It provides a way to identify where users are logged in on a network, **enumerate domain information** such as users, computers, groups, ACLS, trusts, **hunt for file shares and passwords**, **perform Kerberoasting, and more.**

## Domain User Information
- First up is t**he [Get-DomainUser](https://powersploit.readthedocs.io/en/latest/Recon/Get-DomainUser/) function.** 
- This will **provide us with information on all users or specific users we specify.** Below we will use it to grab information about a specific user, `mmorgan`.
	- `Get-DomainUser -Identity mmorgan -Domain inlanefreight.local | Select-Object -Property name,samaccountname,description,memberof,whencreated,pwdlastset,lastlogontimestamp,accountexpires,admincount,userprincipalname,serviceprincipalname,useraccountcontrol`

## Recursive Group Membership
- We can use the **[Get-DomainGroupMember](https://powersploit.readthedocs.io/en/latest/Recon/Get-DomainGroupMember/) function to retrieve group-specific information**.
- Adding the **`-Recurse` switch tells PowerView that if it finds any groups that are part of the target group ==(nested group membership) to list out the members of those groups==**
- `Get-DomainGroupMember -Identity "Domain Admins" -Recurse`
- ![[Pasted image 20250808061742.png]]

## Trust Enumeration
- `Get-DomainTrustMapping`
- ![[Pasted image 20250808061802.png]]

## Testing for Local Admin Access
- We can use the **[Test-AdminAccess](https://powersploit.readthedocs.io/en/latest/Recon/Test-AdminAccess/) function** to test for **local admin access on either the current machine or a remote one.**
- `Test-AdminAccess -ComputerName ACADEMY-EA-MS01`
- ![[Pasted image 20250808061846.png]]

## Finding Users With SPN Set
- Now we can check for users with the **SPN attribute set,** which indicates that the **==account may be subjected to a Kerberoasting attack.==**
- `Get-DomainUser -SPN -Properties samaccountname,ServicePrincipalName`
- ![[Pasted image 20250808061923.png]]

# SharpView
---
- **PowerView** is part of the now **deprecated PowerSploit offensive PowerShell toolkit.**
- Another tool worth **experimenting with is SharpView**, **a .NET port of PowerView.** 
- Many of the same functions supported by PowerView can be used with SharpView. **We can type a method name with `-Help` to get an argument list.**
	- `\SharpView.exe Get-DomainUser -Help`

# Shares
---
- Shares allow users on a domain to **quickly access information relevant to their daily roles and share content with their organization.**
- **Overly permissive shares can potentially cause accidental disclosure of sensitive information,** especially those containing **medical, legal, personnel, HR, data, etc.**
- In an attack, **==gaining control over a standard domain user who can access shares such as the IT/infrastructure shares==** could lead to the **disclosure of sensitive data such as configuration files** or authentication files like SSH keys or passwords stored insecurely.
- We can use **PowerView to hunt for shares and then help us dig through them** or use various manual commands to hunt for common strings such as files with `pass` in the name.
- Now, let's take some time to **explore the tool `Snaffler`** and see how it can aid us in identifying these issues more accurately and efficiently.

# ==Snaffler==
---
- [Snaffler](https://github.com/SnaffCon/Snaffler) is a tool that can **==help us acquire credentials or other sensitive data in an Active Directory environment==**. 
- Snaffler works by **obtaining a list of hosts** within the domain and then **enumerating those hosts for shares and readable directories**.\
- It then **iterates through any directories readable by our user** and **hunts for files that could serve to better** our position within the assessment.

## Snaffler Execution
- `Snaffler.exe -s -d inlanefreight.local -o snaffler.log -v data`
	- The **`-s` tells it to print results** to the console for us, 
	- the **`-d` specifies the domain** to search within, 
	- and the **`-o` tells Snaffler to write results to a logfile**. 
	- The **`-v` option is the verbosity level**
- Typically `data` is best as it only displays results to the screen, so it's easier to begin looking through the tool runs.
- Snaffler can produce a considerable amount of data, so **we should typically output to file and let it run and then come back to it later.**

- We may **==find passwords, SSH keys, configuration files, or other data that can be used to further our access.==** Snaffler color codes the output for us and provides us with a rundown of the file types found in the shares.

# ==BloodHound==
---
- REFER TO **==Domain Enumeration Using Bloodhound in PNPT Preparation Notes==**
- First, **we must authenticate as a domain user from a Windows attack host positioned within the network** (but not joined to the domain) **or transfer the tool to a domain-joined host.**
- If we run SharpHound with the `--help` option, we can see the options available to us.
  
	- `.\SharpHound.exe -c All --zipfilename ILFREIGHT`
	
- Next, **we can exfiltrate the dataset to our own VM** or ingest it into the BloodHound GUI tool on MS01.
- Now, **let's check out a few pre-built queries in the `Analysis` tab.**
- The query **==`Find Computers with Unsupported Operating Systems` is great for finding outdated and unsupported operating systems==** running legacy software.
- Older hosts may be susceptible to older **remote code execution vulnerabilities like [MS08-067](https://support.microsoft.com/en-us/topic/ms08-067-vulnerability-in-server-service-could-allow-remote-code-execution-ac7878fc-be69-7143-472d-2507a179cd15). **
- We can run the **==query `Find Computers where Domain Users are Local Admin` to quickly see if there are any hosts where all users have local admin rights.==**