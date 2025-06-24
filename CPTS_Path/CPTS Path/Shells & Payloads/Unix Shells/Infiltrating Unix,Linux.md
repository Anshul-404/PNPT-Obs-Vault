![[Pasted image 20250624071319.png]]

# Gaining a Shell Through Attacking a Vulnerable Application
---
- Enumerate the Host:
	- `nmap -sC -sV 10.129.201.101`
- ==**`What information could we gather from the output?`**==
- Considering we can see the system is **listening on ports 80 (`HTTP`), 443 (`HTTPS`), 3306 (`MySQL`), and 21 (`FTP`),** it may be safe to assume that **this is a web server hosting a web application.**
- Here we discover a network configuration management tool called [rConfig](https://www.rconfig.com) on web page.
- This application is used by **network & system administrators to automate the process of configuring network appliances**.
- A malicious attacker could own the entire network through rConfig since it will likely have admin access to all the network appliances used to configure.

# Discovering a Vulnerability
---
- We can use the keywords: `rConfig 3.9.6 vulnerability.`

# Search For an Exploit Module
---
- `search rconfig` in metasploit
- This search can point us to the source code for an exploit module called `rconfig_vendors_auth_file_upload_rce.rb`. This exploit can get us a shell session on a target Linux box running rConfig 3.9.6.

# Select an Exploit
---
- `use exploit/linux/http/rconfig_vendors_auth_file_upload_rce`

# Execute the Exploit
---
- `exploit`

# Spawning a TTY Shell with Python
---
- When we drop into the system shell, we notice that no prompt is present, yet we can still issue some system commands.
- his is a shell **typically referred to as a `non-tty shell`.**
- These shells have limited functionality and **can often prevent our use of essential commands like `su` (`switch user`) and `sudo` (`super user do`),** which we will likely need if we seek to escalate privileges.

- `python -c 'import pty; pty.spawn("/bin/sh")' `