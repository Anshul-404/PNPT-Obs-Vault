# Antiviruses
---
- **`sc`:  Service Control**
- `sc query windefend`: **Windows Defender Status**
	- ![[Pasted image 20250412121016.png]]
- `sc queryex type= service`=> **All running services**, we can find some other AV
	- ![[Pasted image 20250412121055.png]]


# Firewalls
---
- `netsh advfirewall firewall dump`
- `netsh firewall show state`
	- ![[Pasted image 20250412121230.png]]
- `netsh firewall show config`: **Open Ports**
	- ![[Pasted image 20250412121303.png]]
	