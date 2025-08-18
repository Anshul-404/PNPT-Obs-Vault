# Enumerating ACLs with PowerView
---
- We can use PowerView to enumerate ACLs, **but the task of digging through _all_ of the results will be extremely time-consuming** and likely inaccurate.

## Using Find-InterestingDomainAcl
- `Find-InterestingDomainAcl`
- If we try to dig through all of this data during a time-boxed assessment, **we will likely never get through it all or find anything interesting** before assessment ends.
  
- Now, there is a way to use a tool such as PowerView more effectively -- by **==performing targeted enumeration starting with a user that we have control over.==**

	- `Import-Module .\PowerView.ps1`
	- `$sid = Convert-NameToSid wley` => Converts name to SID of user we have access to

- We can then **use the `Get-DomainObjectACL` function to perform our targeted search**.
- `SecurityIdentifier` **property which is what tells us _who_ has the given right over an object**

## Using Get-DomainObjectACL
- `Get-DomainObjectACL -Identity * | ? {$_.SecurityIdentifier -eq $sid}`
	- ![[Pasted image 20250810061709.png]]
- We could **Google for the GUID value `00299570-246d-11d0-a768-00aa006e0529`**
- User has the right to force **change the other user's password.**
- Alternatively, **==we could do a reverse search using PowerShell to map the right name back to the GUID value.==**

## Performing a Reverse Search & Mapping to a GUID Value
- `$guid= "00299570-246d-11d0-a768-00aa006e0529"` => FOUND ObjectAceType
- `Get-ADObject -SearchBase "CN=Extended-Rights,$((Get-ADRootDSE).ConfigurationNamingContext)" -Filter {ObjectClass -like 'ControlAccessRight'} -Properties * |Select Name,DisplayName,DistinguishedName,rightsGuid| ?{$_.rightsGuid -eq $guid} | fl`
	- ![[Pasted image 20250810061826.png]]

- PowerView has the **==`ResolveGUIDs` flag, which does this very thing for us.==**
- Notice how the output changes when **we include this flag to show the human-readable format of the `ObjectAceType` property as `User-Force-Change-Password`.**

## Using the -ResolveGUIDs Flag
- `Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid}`
- ![[Pasted image 20250810061920.png]]

## Let's take a quick look at **how we could do this using the [Get-Acl](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.security/get-acl?view=powershell-7.2) and [Get-ADUser](https://docs.microsoft.com/en-us/powershell/module/activedirectory/get-aduser?view=windowsserver2022-ps) cmdlets which we may find available to us on a client system.**
---
## Creating a List of Domain Users
- `Get-ADUser -Filter * | Select-Object -ExpandProperty SamAccountName > ad_users.txt`
## A Useful foreach Loop
- We then read **each line of the file using a `foreach` loop,** and use **the `Get-Acl` cmdlet to retrieve ACL information for each domain user** by feeding each line of the `ad_users.txt` file to the `Get-ADUser` cmdlet. 
- We then **select just the `Access property`, which will give us information about access rights**. 
- Finally, **we set the `IdentityReference` property to the user we are in control of** (or looking to see what rights they have), in our case, `wley`.
	- `foreach($line in [System.IO.File]::ReadLines("C:\Users\htb-student\Desktop\ad_users.txt")) {get-acl  "AD:\$(Get-ADUser $line)" | Select-Object Path -ExpandProperty Access | Where-Object {$_.IdentityReference -match 'INLANEFREIGHT\\wley'}}`
	- ![[Pasted image 20250811053802.png]]

- Once we have this data, **we could follow the same methods shown above to convert the GUID to a human-readable format** to understand what rights we have over the target user.
- Let's use **Powerview to hunt for where, if anywhere, control over the `damundsen` account could take us.**

## Further Enumeration of Rights Using damundsen
- `$sid2 = Convert-NameToSid damundsen`
- `Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid2} -Verbose`
	- ![[Pasted image 20250811053928.png]]
- Now we can see that our **==user `damundsen` has `GenericWrite` privileges over the `Help Desk Level 1` group.**==
- This means, among other things, that **we can add any user (or ourselves) to this group and inherit any rights that this group** has applied to it.

- Let's look and see **if this group is nested into any other groups**, remembering that nested group membership will mean that a**ny users in group A will inherit all rights of any group that group A is nested into (a member of).**

## Investigating the Help Desk Level 1 Group with Get-DomainGroup
- `Get-DomainGroup -Identity "Help Desk Level 1" | select memberof`
- ![[Pasted image 20250811054107.png]]
- `Help Desk Level 1` **group is nested into the `Information Technology` group,** meaning that **we can obtain any rights that the `Information Technology` group grants to its members** if we just add ourselves to the `Help Desk Level 1` group where our user `damundsen` has `GenericWrite` privileges.

![[Pasted image 20250811054144.png]]

## Investigating the Information Technology Group
- Now let's look around and see **if members of `Information Technology` can do anything interesting**
	- `$itgroupsid = Convert-NameToSid "Information Technology"`
	- `Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $itgroupsid} -Verbose`
	- ![[Pasted image 20250811054336.png]]

- Once again, doing our search using `Get-DomainObjectACL` shows us that m**embers of the `Information Technology` group have `GenericAll` rights over the user `adunn`, which means we could:**
	- Modify group membership
	- Force change a password
	- Perform a targeted Kerberoasting attack and attempt to crack the user's password if it is weak

## Looking for Interesting Access for adunn
- Finally, let's see **if the `adunn` user has any type of interesting access that we may be able to leverage to get closer to our goal.**
	- `$adunnsid = Convert-NameToSid adunn `
	- `Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $adunnsid} -Verbose`
	- ![[Pasted image 20250811054506.png]]

- The output above shows that our **==`adunn` user has `DS-Replication-Get-Changes` and `DS-Replication-Get-Changes-In-Filtered-Set` rights over the domain object.==** 
- This means **==that this user can be leveraged to perform a DCSync attack.==** We will cover this attack in-depth in the `DCSync` section.

# Enumerating ACLs with BloodHound
---
- Let's take the data we **gathered earlier with the SharpHound ingestor and upload it to BloodHound.** 
- Next, we can **set the `wley` user as our starting node**
- Select **the `Node Info` tab and scroll down to `Outbound Control Rights`.**
	- This option will **show us objects we have control over directly, via group membership,** and the **number of objects that our user could lead to us controlling via ACL** **attack paths under `Transitive Object Control`.**
	- . If we click **on the `1` next to `First Degree Object Control`**, we see the **first set of rights that we enumerated, `ForceChangePassword` over the `damundsen` user.**
	- ![[Pasted image 20250810062510.png]]
	- If we right-click on the line between the two objects, a menu will pop up. If we select `Help`, we will be presented with help around abusing this ACE
- If we **click on the `16` next to `Transitive Object Control`, we will see the entire path that we painstakingly enumerated above**. From here, we could leverage the help menus for each edge to find ways to best pull off each attack.
- Finally, **we can use the pre-built queries in BloodHound to confirm that the `adunn` user has DCSync rights.**

## Self (Self-Membership) on Group
---
**Another privilege that enables the attacker adding themselves to a group**