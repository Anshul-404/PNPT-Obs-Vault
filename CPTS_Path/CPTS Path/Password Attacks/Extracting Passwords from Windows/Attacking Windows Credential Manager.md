# Windows Vault and Credential Manager
---
- [Credential Manager](https://learn.microsoft.com/en-us/windows-server/security/windows-authentication/credentials-processes-in-windows-authentication#windows-vault-and-credential-manager) is a feature built into Windows since `Server 2008 R2` and `Windows 7`. 
- It allows **users and applications to securely store ==credentials relevant to other systems and websites.==**
- Credentials are stored in **special encrypted folders on the computer under the user and system profiles:**
	- `%UserProfile%\AppData\Local\Microsoft\Vault\`
	- `%UserProfile%\AppData\Local\Microsoft\Credentials\`
	- `%UserProfile%\AppData\Roaming\Microsoft\Vault\`
	- `%ProgramData%\Microsoft\Vault\`
	- `%SystemRoot%\System32\config\systemprofile\AppData\Roaming\Microsoft\Vault\`

- Each vault folder **==contains a `Policy.vpol` file with AES keys (AES-128 or AES-256) that is protected by DPAPI.==**

- The following table lists the two types of credentials Windows stores:
	- ![[Pasted image 20250630055353.png]]

# Enumerating credentials with cmdkey
---
- We can use [cmdkey](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/cmdkey) to enumerate the **credentials stored in the ==current user's profile==:**
	- `cmdkey /list`
	- ![[Pasted image 20250630060804.png]]
	- ![[Pasted image 20250630060839.png]]
- `virtualapp/didlogical`, is a generic credential used by Microsoft account/Windows Live services, it can be ignored.
- `Domain:interactive=SRV01\mcharles`, **is a domain credential associated with the user SRV01\mcharles**. `Interactive` means that the credential is used for interactive logon sessions.
- We can use **==`runas` to impersonate the stored user like so:==**
	- `runas /savecred /user:SRV01\mcharles cmd`

# Extracting credentials with Mimikatz
---
- Even within `mimikatz`, there are multiple ways to attack these credentials - we can either dump credentials from **memory using the `sekurlsa`** module, o**r we can manually decrypt credentials using the `dpapi` module.**
	- `privilege::debug`
	- `sekurlsa::credman`

- **Note:** Some other tools which may be used to enumerate and extract stored credentials included [SharpDPAPI](https://github.com/GhostPack/SharpDPAPI), [LaZagne](https://github.com/AlessandroZ/LaZagne), and [DonPAPI](https://github.com/login-securite/DonPAPI).


# LAB
---
- Login as sadams and totally2brow2harmon@
- Search for saved creds using:
	- `cmdkey /list`
	- ![[Pasted image 20250630070855.png]]
- Get a cmd prompt with mcharles:
	- `runas /savecred /user:SRV01\mcharles cmd.exe`
- mcharles is a local admin but we cannot dump credentials as mimikatz because of UAC, so we need to bypass it
- One common way to bypass is to use `msconfig`
- Start msconfig from cmd prompt and launch cmd prompt from there:
	- `msconfig`
	- ![[Pasted image 20250630071051.png]]
- The spawned cmd prompt **==will still show the current (mcharles) user, but will have elevated privs:==**
	- ![[Pasted image 20250630071227.png]]
- Now we can dump creds from credman using mimikatz
	- ![[Pasted image 20250630071255.png]]