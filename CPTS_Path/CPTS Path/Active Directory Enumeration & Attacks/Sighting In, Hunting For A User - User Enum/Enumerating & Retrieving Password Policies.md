# Enumerating the Password Policy - from Linux - Credentialed
---
- With valid domain credentials, the password policy can be obtained remotely using **tools such as [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) or `rpcclient`.**
- Using crackmapexec:
	- `crackmapexec smb 172.16.5.5 -u avazquez -p Password123 --pass-pol`
	- ![[Pasted image 20250807030012.png]]

# Enumerating the Password Policy - from Linux - SMB NULL Sessions
---
- Without credentials, we may be able to obtain the **==password policy via an SMB NULL session or LDAP anonymous bind.==**
- **SMB NULL sessions allow an unauthenticated attacker** to **retrieve** information from the domain, such as a **complete listing of users, groups, computers, user account attributes, and the domain password policy.**
- When creating a domain in **earlier versions of Windows Server,** **anonymous access was granted** to certain shares, which allowed for domain enumeration.
- We can **use [rpcclient](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html) to check a Domain Controller for SMB NULL** session access.
- Once connected, we can issue an **==RPC command such as `querydominfo`==** to obtain information about the domain and confirm NULL session access.

## Using rpcclient
- `rpcclient -U "" -N 172.16.5.5`
- `querydominfo`
	- ![[Pasted image 20250807030221.png]]
- Obtaining the **Password Policy using rpcclient**:
	- `getdompwinfo`
	- ![[Pasted image 20250807030305.png]]

## enum4linux
---
- `enum4linux` is a **tool built around the [Samba suite of tools](https://www.samba.org/samba/docs/current/man-html/samba.7.html) `nmblookup`, `net`, `rpcclient` and `smbclient`** to use for **enumeration of windows hosts and domains**
- Using enum4linux:
	- `enum4linux -P 172.16.5.5`

- The tool **[enum4linux-ng](https://github.com/cddmp/enum4linux-ng) is a rewrite of `enum4linux` in Python**, but has additional features such as the ability to **export data as YAML or JSON** files which can later be used to process the data further or feed it to other tools.
- It also supports **colored output, among other features**.
- Using enum4linux-ng:
	- `enum4linux-ng -P 172.16.5.5 -oA ilfreight`
- Enum4linux-ng provided us with a bit **clearer output and handy JSON and YAML output using the `-oA` flag.**

# Enumerating Null Session - from Windows
---
- It is **less common to do this type of null session attack from Windows**, but we could use the command
	- `net use \\host\ipc$ "" /u:""` 
- to **establish a null session from a windows machine** and confirm if we can perform more of this type of attack.
- ![[Pasted image 20250807030542.png]]

# Enumerating the Password Policy - from Linux - LDAP Anonymous Bind
---
- [LDAP anonymous binds](https://docs.microsoft.com/en-us/troubleshoot/windows-server/identity/anonymous-ldap-operations-active-directory-disabled) **allow unauthenticated attackers to retrieve information from the domain,** such as a **==complete listing of users, groups, computers, user account attributes, and the domain password policy.==**
- With an LDAP anonymous bind, we can use LDAP-specific enumeration tools such as `windapsearch.py`, `ldapsearch`, `ad-ldapdomaindump.py`, etc., to pull the password policy.

## LDAPSearch
- With [ldapsearch](https://linux.die.net/man/1/ldapsearch), it can be a bit cumbersome but doable. **One example command to get the password policy is as follows:**
	- `ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength`
- Here we can see the minimum password length of 8, lockout threshold of 5, and password complexity is set (`pwdProperties` set to `1`).

# Enumerating the Password Policy - from Windows
---
- If we can **authenticate to the domain from a Windows host**, we can use built-in Windows **==binaries such as `net.exe`==** to retrieve the password policy.
- Using net.exe:
	- `net accounts`
	- ![[Pasted image 20250807030731.png]]
	- This password policy is excellent for password spraying. The **eight-character minimum means that we can try common weak passwords** such as `Welcome1`. 
	- The lockout **threshold of 5 means that we can attempt 2-3** (to be safe) sprays **every 31 minutes** without the risk of locking out any accounts. 
	- If an account has been **locked out, it will automatically unlock** (without manual intervention from an admin) **after 30 minutes**, but **==we should avoid locking out `ANY` accounts at all costs.==**

## Using PowerView
- `import-module .\PowerView.ps1`
- `Get-DomainPolicy`
- PowerView gave us the **same output as our `net accounts` command,** just in a different format but also **revealed that password complexity is enabled** (`PasswordComplexity=1`).

# Analyzing the Password Policy
---
- We've now pulled the password policy in numerous ways. Let's go through the policy for the INLANEFREIGHT.LOCAL domain piece by piece.
- ![[Pasted image 20250807031021.png]]

# Next Steps
---
- Now that we have the password policy in hand, **we need to create a target user list to perform our password spraying attack.**
- Remember that **sometimes we will not be able to obtain the password policy** if we are performing external password spraying (or if we are on an internal assessment and **cannot retrieve the policy using any of the methods shown here).**
- In these cases, **we `MUST` exercise extreme caution** not to lock out accounts.
- We can always ask our client for their password policy if the goal is as comprehensive an assessment as possible