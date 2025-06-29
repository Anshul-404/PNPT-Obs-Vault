# Hunting for Encrypted Files
---
- Many different **extensions correspond to encrypted files**—a useful reference list can be found on [FileInfo](https://fileinfo.com/filetypes/encoded).
- Consider this **command** we might use **to locate commonly encrypted files** on a Linux system:
	- `for ext in $(echo ".xls .xls* .xltx .od* .doc .doc* .pdf .pot .pot* .pp*");do echo -e "\nFile extension: " $ext; find / -name *$ext 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done`
- If we encounter file extensions on a system that we are unfamiliar with, **we can use search engines to research** the technology behind them.

# Hunting for SSH keys
---
- We can use tools like `grep` to recursively search the file system for them during post-exploitation:
	- `grep -rnE '^\-{5}BEGIN [A-Z0-9]+ PRIVATE KEY\-{5}$' /* 2>/dev/null`
	  
- With ==**older PEM formats, it was possible to tell if an SSH key is encrypted**== based on the header, which contains the encryption method in use. 
- ==**Modern SSH keys, however, appear the same**== whether encrypted or not.
- One way to tell w**hether an SSH key is encrypted or not**, is to try **reading the key with `ssh-keygen`**:
	- `ssh-keygen -yf ~/.ssh/id_ed25519 `
	- Attempting to read a password-protected SSH key **will prompt the user for a passphrase:**
		- ![[Pasted image 20250629014244.png]]

# Cracking encrypted SSH keys
---
- We could use the Python script `ssh2john.py` to **acquire the corresponding hash for an encrypted SSH key**, and then use JtR to try and crack it:
	- `ssh2john.py SSH.private > ssh.hash`
	- `john --wordlist=rockyou.txt ssh.hash`
	- `john ssh.hash --show`

# Cracking password-protected documents
---
- Today, most reports, documentation, and information sheets are **commonly distributed as ==Microsoft Office documents or PDFs.==** 
- John the Ripper (JtR) includes a Python **script called ==`office2john.py`**==, which can be used to extract password hashes from ==**all common Office document formats:**==
	- `office2john.py Protected.docx > protected-docx.hash`
	- `john --wordlist=rockyou.txt protected-docx.hash`
	- `john protected-docx.hash --show`

- The process for cracking PDF files is quite similar, **as we simply swap out `office2john.py` for `pdf2john.py`:**
	- `pdf2john.py PDF.pdf > pdf.hash`
	- `john --wordlist=rockyou.txt pdf.hash`
	- `john pdf.hash --show`

# Cracking Protected Archives
---
- Some of the more commonly encountered file extensions include `tar`, `gz`, `rar`, `zip`, `vmdb/vmx`, `cpt`, `truecrypt`, `bitlocker`, `kdbx`, `deb`, `7z`, and `gzip`.

## Cracking ZIP files
- The process of cracking an encrypted ZIP file is similar to what we have seen already, except for using a different script to extract the hashes.
	- `zip2john ZIP.zip > zip.hash`
## Cracking OpenSSL encrypted GZIP files
- To **determine the actual format of a file, we can use the `file` command,** which provides detailed information about its contents. For example:
	- `file GZIP.gzip `: `GZIP.gzip: openssl enc'd data with salted password`
- When cracking OpenSSL encrypted files, **we may encounter various challenges, including numerous false positives or complete failure** to identify the correct password.
- A more reliable approach is to **==use the `openssl` tool within a `for` loop that attempts to extract the contents directly,==** succeeding **only if the correct password is found**:
	- `for i in $(cat rockyou.txt);do openssl enc -aes-256-cbc -d -in GZIP.gzip -k $i 2>/dev/null| tar xz;done`

- Once the `for` loop has finished, **we can check the current directory for a newly extracted file.**

## Cracking BitLocker-encrypted drives
---
- [BitLocker](https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-device-encryption-overview-windows-10) is a **full-disk encryption feature** developed by Microsoft for the Windows operating system.
- It uses the `AES` encryption algorithm with either 128-bit or 256-bit key lengths.
- If the **password or PIN used for BitLocker is forgotten**, decryption can still be performed **using a recovery key—a 48-digit string** generated during the setup process.

- To crack a BitLocker encrypted drive, **==we can use a script called `bitlocker2john` to [four different hashes](https://openwall.info/wiki/john/OpenCL-BitLocker): the first two correspond to the BitLocker password, while the latter two represent the recovery key.==**

- The recovery key is very long and randomly generated, it is **generally not practical to guess**—unless partial knowledge is available. 
- Therefore, we will **focus on cracking the password** **using the first hash** (`$bitlocker$0$...`):
	- `bitlocker2john -i Backup.vhd > backup.hashes`
	- `grep "bitlocker\$0" backup.hashes > backup.hash`
	- `cat backup.hash`
	- `hashcat -a 0 -m 22100 '$bitlocker$0$16$02b329c0453b9273f2fc1b927443b5fe$1048576$12$00b0a67f961dd80103000000$60$d59f37e70696f7eab6b8f95ae93bd53f3f7067d5e33c0394b3d8e2d1fdb885cb86c1b978f6cc12ed26de0889cd2196b0510bbcd2a8c89187ba8ec54f' /usr/share/wordlists/rockyou.txt`

- Since this encryption **uses strong AES encryption, cracking may take considerable time** depending on hardware performance.

## Mounting BitLocker-encrypted drives in Linux (or macOS)
---
- To do this, we can use a tool called [dislocker](https://github.com/Aorimn/dislocker). First, we need to install the package using `apt`:
	- `sudo apt-get install dislocker`
- Next, we create two folders which we will use to mount the VHD:
	- `sudo mkdir -p /media/bitlocker`
	- `sudo mkdir -p /media/bitlockermount`
- We then use `losetup` to configure the VHD as [loop device](https://en.wikipedia.org/wiki/Loop_device), decrypt the drive using `dislocker`, and finally mount the decrypted volume:
	- `sudo losetup -f -P Backup.vhd`
	- `sudo dislocker /dev/loop0p2 -u<CRACKED_PASSWORD> -- /media/bitlocker`
	- `sudo mount -o loop /media/bitlocker/dislocker-file /media/bitlockermount`
- If everything was done correctly, we can now browse the files:
	- `ls -la /media/bitlockermount/`