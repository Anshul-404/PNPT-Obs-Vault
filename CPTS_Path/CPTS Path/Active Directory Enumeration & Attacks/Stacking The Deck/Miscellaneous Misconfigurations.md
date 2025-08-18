# Exchange Related Group Membership
--- 
- A **default installation of Microsoft Exchange within an AD environment** (with no split-administration model) **opens up many attack vectors, as Exchange is often granted considerable privileges** within the domain (via users, groups, and ACLs).
- The group `Exchange Windows Permissions` is not listed as a protected group, but members are granted the ability to **==write a DACL to the domain object.==** This can be leveraged to give a user DCSync privileges.
- An attacker can add accounts to this group by leveraging a DACL misconfiguration (possible) or by leveraging a compromised account that is a member of the Account Operators group.
- **Power users and support staff in remote offices are often added to this group,** allowing them to reset passwords.
- This [GitHub repo](https://github.com/gdedrouas/Exchange-AD-Privesc) details a few techniques for leveraging Exchange for escalating privileges in an AD environment.
- The Exchange **==group `Organization Management` is another extremely powerful group==** (effectively the "Domain Admins" of Exchange) and can **access the mailboxes of all domain users**.
- This group also has full control of the OU called `Microsoft Exchange Security Groups`, which contains the group `Exchange Windows Permissions`.

# PrivExchange
---
- The `PrivExchange` attack results from a flaw in the **Exchange Server `PushSubscription` feature**, which **allows any domain user with a mailbox to force the Exchange server to authenticate to any host provided by the client over HTTP.**
- This flaw can be leveraged to **relay to LDAP and dump the domain NTDS database**. If we cannot relay to LDAP, this can be leveraged to **relay and authenticate to other hosts within the domain**.

# ==Printer Bug==
==---==
- The Printer Bug is a flaw in the **MS-RPRN protocol** (Print System Remote Protocol).
- This protocol defines the communication of print job processing and print system management between a client and a print server.
- To leverage this flaw, **any domain user can connect to the spool's named pipe with the `RpcOpenPrinter` method and use the** `RpcRemoteFindFirstPrinterChangeNotificationEx` method, and **force the server to authenticate to any host provided by the client over SMB.**
- The **spooler service runs as SYSTEM and is installed by defaul**t in Windows servers running Desktop Experience. This attack can be leveraged to **relay to LDAP and grant your attacker account DCSync** privileges to retrieve all password hashes from AD.
- We can use tools such as the `**Get-SpoolStatus` module from [this](http://web.archive.org/web/20200919080216/https://github.com/cube0x0/Security-Assessment) tool** (that can be found on the spawned target) or [this](https://github.com/NotMedic/NetNTLMtoSilverTicket) tool to **check for machines vulnerable to the [MS-PRN Printer Bug](https://blog.sygnia.co/demystifying-the-print-nightmare-vulnerability)**.

## Enumerating for MS-PRN Printer Bug
- `Import-Module .\SecurityAssessment.ps1`
- `Get-SpoolStatus -ComputerName ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL`
	- ![[Pasted image 20250813072947.png]]

# MS14-068
---
- This was a flaw in the **Kerberos protocol**, which could be **leveraged along with standard domain user credentials to elevate privileges to Domain Admin.**
- A **Kerberos ticket contains information about a user, including the account name, ID, and group membership** in the **Privilege Attribute Certificate (PAC).**
- The PAC is **signed by the KDC using secret keys** to validate that the **PAC has not been tampered** with after creation.
- The vulnerability allowed a **forged PAC to be accepted by the KDC as legitimate.**
- This can be leveraged to **create a fake PAC, presenting a user as a member of the Domain Administrators** or other privileged group. **It can be exploited with tools such as the [Python Kerberos Exploitation Kit (PyKEK)](https://github.com/SecWiki/windows-kernel-exploits/tree/master/MS14-068/pykek) or the Impacket toolkit.**

# ==Sniffing LDAP Credentials==
----
- Many applications and printers **store LDAP credentials in their web admin console to connect to the domain.**
- These consoles are **often left with weak or default passwords**
- Sometimes, these credentials **can be viewed in cleartext**. Other times, the a**pplication has a `test connection` function** that we can use to gather credentials by **changing the LDAP IP address to that of our attack host and setting up a `netcat` listener** on LDAP port 389.

# Enumerating DNS Records
---
- We can use a **tool such as [adidnsdump](https://github.com/dirkjanm/adidnsdump)** to **enumerate all DNS records in a domain** using a valid domain user account.
- This is especially helpful if the **naming convention for hosts returned to us in our enumeration using tools such as `BloodHound` is similar to `SRV01934.INLANEFREIGHT.LOCAL`**
- If all servers and workstations have a non-descriptive name, it makes it difficult for us to know what exactly to attack.
- **If we can access DNS entries in AD**, we can potentially **discover interesting DNS records** that point to this same server, s**uch as `JENKINS.INLANEFREIGHT.LOCAL`**, which we can use to better plan out our attacks.
- The tool works because, **by default, all users can list the child objects of a DNS zone in an AD environment**
- So by using the `adidnsdump` tool, **we can resolve all records in the zone and potentially find something useful for our engagement.**

## Using adidnsdump
- `adidnsdump -u inlanefreight\\forend ldap://172.16.5.5 `
- ![[Pasted image 20250813073552.png]]
- If we run again with the **`-r` flag the tool will attempt to resolve unknown records by performing an `A` query**. 
- Now **we can see that an IP address of `172.16.5.240`** showed up for LOGISTICS
	- `adidnsdump -u inlanefreight\\forend ldap://172.16.5.5 -r`
- ![[Pasted image 20250813073636.png]]

# Other Misconfigurations
---
## Password in Description Field
- Sensitive information **such as account passwords are sometimes found in the user account `Description` or `Notes` fields** and can be quickly enumerated using PowerView:
	- `Get-DomainUser * | Select-Object samaccountname,description |Where-Object {$_.Description -ne $null}`

# ==PASSWD_NOTREQD Field==
---
- It is possible to come across domain accounts with the **[passwd_notreqd](https://ldapwiki.com/wiki/Wiki.jsp?page=PASSWD_NOTREQD) field set in the userAccountControl attribute.**
- If this is **==set, the user is not subject to the current password policy length,==** meaning **they could have a shorter password or no password at all** (if empty passwords are allowed in the domain).
- Just because this flag is set on an account, it d**oesn't mean that no password is set,** **just that one may not be required.**
## Checking for PASSWD_NOTREQD Setting using Get-DomainUser
- `Get-DomainUser -UACFilter PASSWD_NOTREQD | Select-Object samaccountname,useraccountcontrol`
- ![[Pasted image 20250813073848.png]]

# Credentials in SMB Shares and SYSVOL Scripts
---
- The **SYSVOL share can be a treasure trove of data,** especially in large organizations.
- We may find many **different batch, VBScript, and PowerShell scripts within the scripts directory,** which is readable by all authenticated users in the domain.
- It is **==worth digging around this directory to hunt for passwords stored==** in scripts.
- Here, we can see an interesting script named `reset_local_admin_pass.vbs`:
	- `ls \\academy-ea-dc01\SYSVOL\INLANEFREIGHT.LOCAL\scripts`

- We could do this **using CrackMapExec and the `--local-auth` flag as shown** in this module's `Internal Password Spraying - from Linux` section:
	- `cat \\academy-ea-dc01\SYSVOL\INLANEFREIGHT.LOCAL\scripts\reset_local_admin_pass.vbs`
	- ![[Pasted image 20250813074029.png]]

# ==Group Policy Preferences (GPP) Passwords==
---
- When a **new GPP is created, an .xml file is created in the SYSVOL share**, which is also **cached locally on endpoints that the Group Policy applies to**. These files can include those used to:
	- Map drives (drives.xml)
	- Create local users
	- Create printer config files (printers.xml)
	- Creating and updating services (services.xml)
	- Creating scheduled tasks (scheduledtasks.xml)
	- Changing local admin passwords.
- These files **can contain an array of ==configuration data and defined passwords.**==
- The **`cpassword` attribute value is AES-256 bit encrypted**, but Microsoft [published the AES private key on MSDN](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-gppref/2c15cbf0-f086-4c74-8b70-1f2fa45dd4be?redirectedfrom=MSDN), **which can be used to decrypt the password.**
- **Any domain user can read these files as they are stored on the SYSVOL share**, and all authenticated users in a domain, by default, have read access to this domain controller share.
- This was **patched in 2014** [MS14-025 Vulnerability in GPP could allow elevation of privilege](https://support.microsoft.com/en-us/topic/ms14-025-vulnerability-in-group-policy-preferences-could-allow-elevation-of-privilege-may-13-2014-60734e15-af79-26ca-ea53-8cd617073c30), **to prevent administrators from setting passwords** using GPP.
- The patch **does not remove existing Groups.xml files with passwords from SYSVOL**. **If you delete the GPP policy instead of unlinking it from the OU, the cached copy on the local computer remains.**
	- ![[Pasted image 20250813074514.png]]
- Decrypting the Password with gpp-decrypt:
	- `gpp-decrypt VPe/o9YRyz2cksnYRbNeQj35w9KxQ5ttbvtRaAVqxaE`

- **GPP passwords** can be located by **searching or manually browsing the SYSVOL share or using tools such as** [Get-GPPPassword.ps1](https://github.com/PowerShellMafia/PowerSploit/blob/master/Exfiltration/Get-GPPPassword.ps1)
- **CrackMapExec also has two modules for locating and retrieving GPP passwords.** 
- One quick tip to consider during engagements: Often, **GPP passwords are defined for legacy accounts, and ==you may therefore retrieve and decrypt the password for a locked or deleted account.**==
- However, it is **==worth attempting to password spray internally with this password (especially if it is unique)==**

- Locating & Retrieving GPP Passwords with CrackMapExec:
	- `crackmapexec smb -L | grep gpp`
- It is also possible to **find passwords in files such as Registry.xml when autologon is configured via Group Policy.**
- This may be set up for any number of reasons for a machine to automatically log in at boot. 
- If this is **set via Group Policy and not locally on the host, then anyone on the domain can retrieve credentials stored in the Registry.xml** file created for this purpose.
- We can hunt for this **using CrackMapExec with the [gpp_autologin](https://www.infosecmatter.com/crackmapexec-module-library/?cmem=smb-gpp_autologin) module, or using the [Get-GPPAutologon.ps1](https://github.com/PowerShellMafia/PowerSploit/blob/master/Exfiltration/Get-GPPAutologon.ps1) script** included in PowerSploit.
	- `crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 -M gpp_autologin`
	- ![[Pasted image 20250813075314.png]]
- In the output above, we can see that we have **retrieved the credentials for an account called `guarddesk`.**
- This may have been set up so that **shared workstations used by guards automatically log in at boot to accommodate multiple users** throughout the day and night working different shifts.

# ==ASREPRoasting==
---
- It's possible to obtain the **Ticket Granting Ticket (TGT) for any account that has the [Do not require Kerberos pre-authentication](https://www.tenable.com/blog/how-to-stop-the-kerberos-pre-authentication-attack-in-active-directory) setting enabled.**
- Many vendor installation guides specify that their service account be configured in this way.
- The ==**authentication service reply (AS_REP)== is encrypted with the account’s password, and any domain user can request it.**
  
- **With pre-authentication**, **a user enters their password, which encrypts a time stamp**. 
	- **The Domain Controller will decrypt this to validate that the correct password was used**. If successful, **a TGT will be issued to the user for further authentication requests** in the domain.
- If an **account has pre-authentication disabled**, an attacker can **request authentication data for the affected account and retrieve an encrypted TGT from the Domain Controller.**
- **==ASREPRoasting is similar to Kerberoasting, but it involves attacking the AS-REP instead of the TGS-REP.==** An SPN is not required.
- This setting can be enumerated with PowerView or built-in tools such as the PowerShell AD module.
- The **attack itself can be performed with the [Rubeus](https://github.com/GhostPack/Rubeus) toolkit and other tools to obtain the ticket for the target account**. 
- If an attacker has **`GenericWrite` or `GenericAll` permissions over an account**, they can **enable this attribute and obtain the AS-REP ticket** for offline cracking to recover the account's password before disabling the attribute again.

## Enumerating for DONT_REQ_PREAUTH Value using Get-DomainUser
- `Get-DomainUser -PreauthNotRequired | select samaccountname,userprincipalname,useraccountcontrol | fl`
- ![[Pasted image 20250814053504.png]]
- With this information in hand, the Rubeus tool can be leveraged to retrieve the AS-REP in the proper format for offline hash cracking.
- Remember, **add the `/nowrap` flag so the ticket is not column wrapped** and is retrieved in a **format that we can readily feed into Hashcat.**

## Retrieving AS-REP in Proper Format using Rubeus
- `.\Rubeus.exe asreproast /user:mmorgan /nowrap /format:hashcat`
- ![[Pasted image 20250814053552.png]]

## ==Retrieving the AS-REP Using Kerbrute==
- `kerbrute userenum -d inlanefreight.local --dc 172.16.5.5 /opt/jsmith.txt `
- ![[Pasted image 20250814053655.png]]

- With a **==list of valid users, we can use [Get-NPUsers.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/GetNPUsers.py) from the Impacket toolkit to hunt for all users with Kerberos pre-authentication not required.==**
- We can also feed a **wordlist such as `jsmith.txt` into the tool,** it will throw errors for users that do not exist, but if it finds any valid ones without Kerberos pre-authentication, then it can be a nice way to obtain a foothold or further our access.

## ==Hunting for Users with Kerberos Pre-auth Not Required==
- `GetNPUsers.py INLANEFREIGHT.LOCAL/ -dc-ip 172.16.5.5 -no-pass -usersfile valid_ad_users `
- ![[Pasted image 20250814053806.png]]

# Group Policy Object (GPO) Abuse
--- 
- Group Policy Objects (GPOs) are ==**collections of settings that control how computers and users behave** within a Microsoft Active Directory environment==.
- Group Policy provides administrators with many advanced settings that can be applied to both user and computer objects in an AD environment.
- If we can gain rights over a Group Policy Object via an ACL misconfiguration, we could leverage this for **lateral movement, privilege escalation, and even domain compromise** and as a persistence mechanism within the domain.
  
- GPO misconfigurations **can be abused to perform the following attacks:**
	- **Adding additional rights to a user** (such as SeDebugPrivilege, SeTakeOwnershipPrivilege, or SeImpersonatePrivilege)
	- **Adding a local admin user** to one or more hosts
	- **Creating an immediate scheduled task** to perform any number of actions
- We can enumerate GPO information using many of the **tools we've been using throughout this module such as PowerView and BloodHound**. We can also use [group3r](https://github.com/Group3r/Group3r), [ADRecon](https://github.com/sense-of-security/ADRecon), [PingCastle](https://www.pingcastle.com/), among others, to audit the security of GPOs in a domain.

## Enumerating GPO Names with PowerView
- Using the **[Get-DomainGPO](https://powersploit.readthedocs.io/en/latest/Recon/Get-DomainGPO) function from PowerView**, we can get a listing of GPOs by name:
	- `Get-DomainGPO |select displayname`
	- ![[Pasted image 20250814054043.png]]
- This can be helpful for us to begin to **see what types of security measures are in place** (such as denying cmd.exe access and a separate password policy for service accounts).
- We can see that **autologon is in use which may mean there is a readable password in a GPO**, and see that **Active Directory Certificate Services (AD CS) is present in the domain.**

## Enumerating GPO Names with a Built-In Cmdlet
- `Get-GPO -All | Select DisplayName`
- ![[Pasted image 20250814054148.png]]

## Enumerating Domain User GPO Rights
- A good first check is to see **==if the entire Domain Users group has any rights over one or more GPOs.==**
  
	- `$sid=Convert-NameToSid "Domain Users"`
	- `Get-DomainGPO | Get-ObjectAcl | ?{$_.SecurityIdentifier -eq $sid}`
	- ![[Pasted image 20250814054242.png]]
	- Here we can see that the **Domain Users group has various permissions over a GPO, such as `WriteProperty` and `WriteDacl`, which we could leverage to give ourselves full control over the GPO** and pull off any number of attacks that would be pushed down to any users and computers in OUs that the GPO is applied to.
## Converting GPO GUID to Name
- `Get-GPO -Guid 7CA9C789-14CE-46E3-A722-83F4097AF532`

## Bloodhound
- Checking in BloodHound, we can see that the **`Domain Users` group has several rights over the `Disconnect Idle RDP` GPO, which could be leveraged for full control of the object.**
- ![[Pasted image 20250814054442.png]]
- We could use a t**ool such as [SharpGPOAbuse](https://github.com/FSecureLABS/SharpGPOAbuse) to take advantage of this GPO misconfiguration** by performing actions such as **adding a user that we control to the local admins group on one of the affected hosts,** creating an **immediate scheduled task** on one of the hosts to give us a reverse shell, or configure a malicious computer startup script to provide us with a reverse shell or similar.
- If we **found an editable GPO that applies to an OU with 1,000 computers**, we **would not want to make the mistake of adding ourselves as a local admin to that many hosts**. 
	- Organizational Units (OUs) are **==containers within Active Directory (AD) or AWS Organizations used to logically group and manage objects like users, computers, and other resources==**
- Some of the attack options available with this tool allow us to specify a target user or host. The hosts shown in the above image are not exploitable, and GPO attacks will be covered in-depth in a later module.