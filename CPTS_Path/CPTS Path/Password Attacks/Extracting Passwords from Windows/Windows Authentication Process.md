- The [Windows client authentication process](https://docs.microsoft.com/en-us/windows-server/security/windows-authentication/credentials-processes-in-windows-authentication) involves multiple modules responsible for logon, credential retrieval, and verification.
- Among the various authentication mechanisms in Windows, ==**Kerberos is one of the most widely used and complex**.== 
- The [Local Security Authority](https://learn.microsoft.com/en-us/windows-server/security/credentials-protection-and-management/configuring-additional-lsa-protection) **(`LSA`) is a protected subsystem that authenticates users, manages local logins, oversees all aspects of local security**, and provides services for translating between user names and security identifiers (SIDs). The security subsystem **maintains security policies and user accounts** on a computer system.
- On a Domain Controller, **these policies and accounts apply to the entire domain** and are **stored in Active Directory.**
	- ![[Pasted image 20250629074207.png]]


- **`WinLogon` is a trusted system process** responsible for managing s**ecurity-related user interactions**, such as:
	- Launching **`LogonUI` to prompt for credentials at login**
	- Handling password changes
	- Locking and unlocking the workstation
- To **obtain a user's account name and password, WinLogon relies on credential providers installed on the system.** These credential providers are `COM` objects implemented as DLLs.
- WinLogon is the only process that intercepts login requests from the keyboard, which are sent via RPC messages from `Win32k.sys`. 
- At logon, **it immediately launches the `LogonUI` application to present the graphical user interface.** 
- Once the user's credentials are collected by the credential provider, **WinLogon passes them to the Local Security Authority Subsystem Service (`LSASS`) to authenticate the user.**

# LSASS
---
- The [Local Security Authority Subsystem Service](https://en.wikipedia.org/wiki/Local_Security_Authority_Subsystem_Service) (`LSASS`) is comprised of **multiple modules and governs all authentication processes**.
- It **authenticates all kinds of access requests**, not just logins at the login screen.
- Located at `%SystemRoot%\System32\Lsass.exe`in the file system, it is responsible for **enforcing the local security policy, authenticating users, and forwarding security audit logs to the `Event Log`.**
- In essence, LSASS serves as the **gatekeeper in Windows-based operating systems.**

# SAM database
---
- The Security Account Manager (SAM) is a database file in Windows operating systems **that stores user account credentials**. 
- It is used to **authenticate both local and remote users** and uses cryptographic protections to prevent unauthorized access. 
- User passwords are stored as hashes in the registry, **typically in the form of either `LM` or `NTLM` hashes.**
- he SAM file is ==**located at `%SystemRoot%\system32\config\SAM` and is mounted under `HKLM\SAM`**==. Viewing or accessing this file requires `SYSTEM` level privileges.

- If the ==**system has been joined to a domain, the Domain Controller (`DC`) must validate the credentials from the Active Directory database (`ntds.dit`)**==, which is stored in `%SystemRoot%\ntds.dit`.

# Credential Manager
---
- Credential Manager is a built-in feature of all Windows operating systems that allows users to **store and manage credentials used to access network resources, websites, and applications.** 
- These saved credentials are **stored per user profile in the user's `Credential Locker`**. The credentials are encrypted and stored at the following location:
	- `C:\Users\[Username]\AppData\Local\Microsoft\[Vault/Credentials]\`

# NTDS
---
- Each Domain Controller **hosts a file called `NTDS.dit`, which is synchronized across all Domain Controllers,** with the exception of [Read-Only Domain Controllers (RODCs)](https://docs.microsoft.com/en-us/windows/win32/ad/rodc-and-active-directory-schema).
- `NTDS.dit` is a database file that stores Active Directory data, including but not limited to:
	- **User accounts (username & password hash)**
	- **Group accounts**
	- **Computer accounts**
	- **Group policy objects**

# Summary
---
![[Pasted image 20250629075142.png]]