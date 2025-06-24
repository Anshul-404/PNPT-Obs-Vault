# Prominent Windows Exploits
---
![[Pasted image 20250624062749.png]]

# Enumerating Windows & Fingerprinting Methods
---
## Pinged Host
---
- `ping 192.168.86.39 `
	- A typical response from a Windows host will either be 32 or 128. A response of or around 128 is the most common response you will see.
- OS Detection Scan:
	- `sudo nmap -v -O 192.168.86.39`
- Banner Grab to Enumerate Ports: 
	- `sudo nmap -v 192.168.86.39 --script banner.nse`
	- For each port Nmap sees as up, it will attempt to connect to the port and glean any information it can from it.

# Bats, DLLs, & MSI Files, Oh My!
---
- When it comes to creating payloads for Windows hosts, we have plenty of options to choose from.
- Payload Types to Consider:
- ![[Pasted image 20250624063246.png]]

# Tools, Tactics, and Procedures for Payload Generation, Transfer, and Execution
---
## Payload Generation
---
![[Pasted image 20250624063318.png]]

## Payload Transfer and Execution:
---
![[Pasted image 20250624063359.png]]

# CMD vs PowerShell
---
![[Pasted image 20250624063544.png]]