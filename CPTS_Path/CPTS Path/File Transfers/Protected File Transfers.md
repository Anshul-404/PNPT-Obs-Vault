- As penetration testers, we often gain access to highly sensitive data such as user lists, credentials (i.e., **downloading the NTDS.dit file** for offline password cracking), and enumeration data that can contain critical information about the **organization's network infrastructure, and Active Directory (AD) environment**, etc.
- Therefore, it is **essential to encrypt this data** or use **encrypted data connections such as SSH, SFTP, and HTTPS.**

# File Encryption on Windows
---
- Many different methods can be used to encrypt files and information on Windows systems. 
- **==One of the simplest methods is the [Invoke-AESEncryption.ps1](https://www.powershellgallery.com/packages/DRTools/4.0.2.3/Content/Functions%5CInvoke-AESEncryption.ps1) PowerShell script.==** 
- This script is small and provides encryption of files and strings.
- We can use any previously shown file transfer methods to get this file onto a target host. After the script has been transferred, it only needs to be i**mported as a module, as shown below:**
	- `Import-Module .\Invoke-AESEncryption.ps1`

- This command creates an encrypted file with the same name as the **encrypted file but with the extension "`.aes`."**:
	- `Invoke-AESEncryption -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt`
	- ![[Pasted image 20250623064443.png]]

# File Encryption on Linux
---
- [OpenSSL](https://www.openssl.org/) is frequently included in Linux distributions, with sysadmins using it to generate security certificates, among other tasks. OpenSSL can be used to send files "nc style" to encrypt files.
- Let's use `-aes256` as an example. 
- We can also override the default iterations counts with the option `-iter 100000` and add the option `-pbkdf2` to use the Password-Based Key Derivation Function 2 algorithm.

- Encrypting /etc/passwd with openssl:
	- `openssl enc -aes256 -iter 100000 -pbkdf2 -in /etc/passwd -out passwd.enc`
	- When we hit enter, **we'll need to provide a password.**
- Decrypt passwd.enc with openssl:
	- `openssl enc -d -aes256 -iter 100000 -pbkdf2 -in passwd.enc -out passwd `