# Cross-Forest Kerberoasting
---
- Kerberos attacks such as **Kerberoasting and ASREPRoasting can be performed across trusts**, depending on the trust direction.
- In a situation where **you are positioned in a domain with either an inbound or bidirectional domain/forest trust**, you can likely p**erform various attacks to gain a foothold.**
- We can **utilize PowerView to enumerate accounts in a target domain** that have SPNs associated with them:

## Enumerating Accounts for Associated SPNs Using Get-DomainUser
- `Get-DomainUser -SPN -Domain FREIGHTLOGISTICS.LOCAL | select SamAccountName`
	- ![[Pasted image 20250817061247.png]]
- We see that t**here is one account with an SPN in the target domain**. A quick check shows that thi**s account is a member of the Domain Admins group** in the target domain.

## Enumerating the mssqlsvc Account
- `Get-DomainUser -Domain FREIGHTLOGISTICS.LOCAL -Identity mssqlsvc |select samaccountname,memberof`
- ![[Pasted image 20250817061319.png]]
- Let's perform a **Kerberoasting attack across the trust using `Rubeus`**

## Performing a Kerberoasting Attacking with Rubeus Using /domain Flag
- We run the tool as we did in the Kerberoasting section, **==but we include the `/domain:` flag and specify the target domain.==**
- `.\Rubeus.exe kerberoast /domain:FREIGHTLOGISTICS.LOCAL /user:mssqlsvc /nowrap`
- ![[Pasted image 20250817061357.png]]
## Admin Password Re-Use & Group Membership
---
- From time to time, **we'll run into a situation where there is a bidirectional forest trust managed by admins from the same company.**
- If **we can take over Domain A and obtain cleartext passwords or NT hashes for either the built-in Administrator account** (or an account that is part of the Enterprise Admins or Domain Admins group in Domain A), and **==Domain B has a highly privileged account with the same name, then it is worth checking for password reuse across the two forests.==**
- We may also see users or admins from Domain A as members of a group in Domain B. Only `Domain Local Groups` allow security principals from outside its forest.
- We may see a **Domain Admin or Enterprise Admin from Domain A as a member of the built-in Administrators group in Domain B in a bidirectional forest trust relationship.**
- If we **can take over this admin user in Domain A**, we **would gain full administrative access to Domain B based on group membership**.

## Using Get-DomainForeignGroupMember
- We can use the **PowerView function [Get-DomainForeignGroupMember](https://powersploit.readthedocs.io/en/latest/Recon/Get-DomainForeignGroupMember) to enumerate groups with users that do not belong to the domain**, **also known as `foreign group membership`.**

- `Get-DomainForeignGroupMember -Domain FREIGHTLOGISTICS.LOCAL`
	- ![[Pasted image 20250817061623.png]]
- The above command output shows that the **==built-in Administrators group in `FREIGHTLOGISTICS.LOCAL` has the built-in Administrator account for the `INLANEFREIGHT.LOCAL` domain as a member.==**

## Accessing DC03 Using Enter-PSSession
-  We can verify this access using the **`Enter-PSSession` cmdlet to connect over WinRM.**
- ` Enter-PSSession -ComputerName ACADEMY-EA-DC03.FREIGHTLOGISTICS.LOCAL -Credential INLANEFREIGHT\administrator`
- ![[Pasted image 20250817061812.png]]

- From the command output above, **==we can see that we successfully authenticated to the Domain Controller in the `FREIGHTLOGISTICS.LOCAL` domain using the Administrator account from the `INLANEFREIGHT.LOCAL` domain across the bidirectional forest trust.==**
- This can be a quick win after taking control of a domain and is **always worth checking for if a bidirectional forest trust situation** is present during an assessment and the second forest is in-scope.

# SID History Abuse - Cross Forest
---
![[Pasted image 20250817061910.png]]