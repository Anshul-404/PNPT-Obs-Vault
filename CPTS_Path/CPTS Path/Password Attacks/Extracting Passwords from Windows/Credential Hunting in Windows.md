- `Credential hunting` is the process of performing detailed searches across the file system and through various applications to discover credentials.

# Search-centric
---
- `What might an IT admin be doing on a day-to-day basis and which of those tasks may require credentials?`
- A user may have **documented their passwords somewhere on the system.** 
- There may even be **default credentials that could be found in various files.**

## ==Key terms to search for==
- Some helpful key terms we can use that can help us discover some credentials:
	- Passwords
	- Passhrases
	- Keys
	- Username
	- User account
	- Creds
	- Users
	- Passkeys
	- configuration
	- dbcredential
	- dbpassword
	- pwd
	- Login
	- Credentials

# Search tools
---
## Windows Search
- With **access to the GUI, it is worth attempting to use `Windows Search`** to find files on the target using some of the keywords mentioned above.

## ==LaZagne==
- We can also take advantage of third-party tools like [LaZagne](https://github.com/AlessandroZ/LaZagne) to **quickly ==discover credentials that web browsers or other installed applications**== may insecurely store.
- LaZagne is **made up of `modules`** which each target different software when looking for passwords. Some of the common modules are described in the table below:
	- ![[Pasted image 20250701052616.png]]
- **Note:** Web browsers are some of the most interesting placed to search for credentials, due to the fact that many of them offer built-in credential storage.

- Running LaZagne:
	- `start LaZagne.exe all`
	- This will execute LaZagne and run `all` included modules.
	- If we used the `-vv` option, we would see attempts to gather passwords from all LaZagne's supported software.

## findstr
- We can also use [findstr](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/findstr) to search from patterns across many types of files:
	- `findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml`

# Additional considerations
---
- Know that which ones **we choose to use will be primarily based on the function of the computer.** 
- If we land on a Windows Server, we may use a different approach than if we land on a Windows Desktop.
- Always be mindful of **==how the system is being used, and this will help us know where to look.==**
- We may even be able to **find credentials by navigating and listing directories on the file system as our tools run.**
- **==Here are some other places we should keep in mind when credential hunting:==**

	- **Passwords in Group Policy in the SYSVOL share**
	- **Passwords in scripts in the SYSVOL share**
	- **Password in scripts on IT shares**
	- **Passwords in `web.config` files on dev machines and IT shares**
	- **Password in `unattend.xml`**
	- **Passwords in the AD user or computer description fields**
	- **KeePass databases (if we are able to guess or crack the master password)**
	- **Found on user systems and shares**
	- **Files with names like `pass.txt`, `passwords.docx`, `passwords.xlsx` found on user systems, shares, and [Sharepoint](https://www.microsoft.com/en-us/microsoft-365/sharepoint/collaboration)**

# Labs
---
=> What credentials does Bob use with WinSCP to connect to the file server? (Format: username:password, Case-Sensitive)
- Download https://github.com/anoopengineer/winscppasswd and transfer the binary to windows server
- Get the saved winscp credentials from registry using:
	- `reg export "HKCU\Software\Martin Prikryl\WinSCP 2\Sessions" sessions.reg`
- Run the tool to extract the password:
	- `.\winscppasswd.exe Laptop01 bob A35C474A426552B02835829E6FB1F3474E01197AA633AB047690293E293228296D6C726D6C726D6972656F1A0F3D383135326D6E6F`
	- ![[Pasted image 20250701060002.png]]
