# Detailed User Enumeration
---
- To mount a successful password spraying attack, **we first need a list of valid domain users** to attempt to authenticate with. There are several ways that we can gather a target list of valid users:
	- By leveraging an **SMB NULL session to retrieve** a complete list of domain users from the domain controller
	- Utilizing an **LDAP anonymous bind** to query **LDAP anonymously** and pull down the domain user list
	- Using a **tool such as `Kerbrute` to validate users** utilizing a **word list from a source such as the [statistically-likely-usernames](https://github.com/insidetrust/statistically-likely-usernames)** GitHub repo, or gathered by using a tool such as [linkedin2username](https://github.com/initstring/linkedin2username) to create a list of potentially valid users
	- Using a **set of credentials from a Linux or Windows attack system** either **provided by our client** or obtained through another means **such as LLMNR/NBT-NS response poisoning using `Responder`** or even a successful password spray using a smaller wordlist

- No matter the method we choose, it is also vital for us to **consider the domain password policy.**
- Having this policy in hand is very useful because the **minimum password length** and whether or not **password complexity** is enabled can help us **formulate the list**.
- Again, if **we do not know the password policy**, **we can always ask our client, and, if they won't provide it,** we can either try one very targeted password spraying attempt as a "hail mary" if all other options for a foothold have been exhausted.
- ![[Pasted image 20250807053612.png]]

# SMB NULL Session to Pull User List
---
- If you are on an internal machine but don’t have valid domain credentials, you can look for **SMB NULL sessions or LDAP anonymous binds** on Domain Controllers.
- If you **already have credentials for a domain user or `SYSTEM` access** on a Windows host, **then you can easily query Active Directory for this information.**
- It’s possible **==to do this using the SYSTEM account because it can `impersonate` the computer.==**
- A **computer object is treated as a domain user account** (with some differences, such as authenticating across forest trusts)
- Some **tools that can leverage SMB NULL sessions and LDAP anonymous** binds include [enum4linux](https://github.com/portcullislabs/enum4linux), [rpcclient](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html), and [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec), among others.

## Using enum4linux:
- `enum4linux -U 172.16.5.5  | grep "user:" | cut -f2 -d"[" | cut -f1 -d"]"`
## Using rpcclient:
- `rpcclient -U "" -N 172.16.5.5`
## Using CrackMapExec --users Flag
- Finally, we can use `CrackMapExec` with the `--users` flag. 
- This is a useful tool that will also **show the `badpwdcount` (invalid login attempts)**
- It also shows the **`baddpwdtime`, which is the date and time of the last bad password attempt**
	- `crackmapexec smb 172.16.5.5 --users`

# Gathering Users with LDAP Anonymous
---
- We can use various tools to gather users when we find an LDAP anonymous bind. Some examples include [windapsearch](https://github.com/ropnop/windapsearch) and [ldapsearch](https://linux.die.net/man/1/ldapsearch)
## Using ldapsearch
- `ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "(&(objectclass=user))"  | grep sAMAccountName: | cut -f2 -d" "`

## Using windapsearch
- Tools such as **`windapsearch` make this easier** (though we should still understand how to create our own LDAP search filters).
- Here we **can specify anonymous access by providing a blank username** with the `-u` flag and the **`-U` flag to tell the tool to retrieve just users.**
	- `./windapsearch.py --dc-ip 172.16.5.5 -u "" -U`

# Enumerating Users with Kerbrute
---
- This tool uses [Kerberos Pre-Authentication](https://ldapwiki.com/wiki/Wiki.jsp?page=Kerberos%20Pre-Authentication), which is a much faster and potentially stealthier way to perform password spraying.
- This method **does not generate Windows event ID [4625: An account failed to log on](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4625), or a logon failure** which is often monitored for.
- The **==tool sends TGT requests to the domain controller without Kerberos Pre-Authentication==** to perform username enumeration.
- **==If the KDC responds with the error `PRINCIPAL UNKNOWN`, the username is invalid.==**

- Let's try out this **method using the [jsmith.txt](https://github.com/insidetrust/statistically-likely-usernames/blob/master/jsmith.txt) wordlist of 48,705 possible** common usernames in the format `flast`. T**he [statistically-likely-usernames](https://github.com/insidetrust/statistically-likely-usernames) GitHub repo is an excellent resource** for this type of attack.

## Kerbrute User Enumeration
- `kerbrute userenum -d inlanefreight.local --dc 172.16.5.5 /opt/jsmith.txt `

If we are unable to create a valid username list using any of the methods highlighted above, we could turn back to **external information gathering and search for company email addresses or use a tool such as [linkedin2username**](https://github.com/initstring/linkedin2username) to mash up possible usernames from a company's LinkedIn page.

# Credentialed Enumeration to Build our User List
---
- With valid credentials, we can use any of the tools stated previously to build a user list. A quick and easy way is using CrackMapExec:

- Using CrackMapExec with Valid Credentials:
	- `sudo crackmapexec smb 172.16.5.5 -u htb-student -p Academy_student_AD! --users`

# LAB
---
=> Enumerate valid usernames using Kerbrute and the wordlist located at /opt/jsmith.txt on the ATTACK01 host. How many valid usernames can we enumerate with just this wordlist from an unauthenticated standpoint?

- `kerbrute userenum -d inlanefreight.local --dc 172.16.5.5 /opt/jsmith.txt | grep VALID | wc -l`
- ![[Pasted image 20250807054700.png]]