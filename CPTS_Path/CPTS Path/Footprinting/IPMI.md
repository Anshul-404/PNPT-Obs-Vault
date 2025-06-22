- **Intelligent Platform Management Interface (IPMI)** is a set of standardized specifications for hardware-based host management systems used for system management and monitoring. 
- IPMI provides sysadmins with the ability to manage and monitor systems even if they are powered off or in an unresponsive state. 
- It operates using a direct network connection to the system's hardware and does not require access to the operating system via a login shell. 
- IPMI is typically used in three ways:

	- Before the OS has booted to modify BIOS settings
	- When the host is fully powered down
	- Access to a host after a system failure

# Footprinting
---
- IPMI communicates over **port 623 UDP.**
- ` nmap -sU --script ipmi-version -p 623 ilo.inlanfreight.local`
- We can also use the Metasploit scanner module [IPMI Information Discovery (auxiliary/scanner/ipmi/ipmi_version)](https://www.rapid7.com/db/modules/auxiliary/scanner/ipmi/ipmi_version/).
- ` use auxiliary/scanner/ipmi/ipmi_version `

# Default Passwords
---
![[Pasted image 20250617071423.png]]


# Dangerous Settings
---
![[Pasted image 20250617071450.png]]

# Metasploit Dumping Hashes
---
- `Module options (auxiliary/scanner/ipmi/ipmi_dumphashes):`