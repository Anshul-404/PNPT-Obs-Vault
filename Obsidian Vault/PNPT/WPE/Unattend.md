# Information
---
- What is `unattend.xml`?
	- It’s used during **automated Windows installations** (unattended setups).
	- Sysadmins use it to configure settings like:
	    - **Admin account creation**
	    - **Passwords**
	    - Domain joins
	    - **Scripts to run on first boo**t
- The file tells Windows **how to install without manual intervention**.
- Sometimes, admins **hardcode plaintext passwords** in it for the admin/local account.
- Found In:
```
C:\Windows\Panther\Unattend\Unattend.xml
C:\Windows\Panther\Unattend.xml
C:\Windows\System32\Sysprep\Unattend.xml
```


# Exploitation
---
- We can use metasploit's `post/windows/gather/enum_unattend`:
	- ![[Pasted image 20250418172253.png]]
- Or with `PowerUp.ps1`or manually:
	- `type C:\Windows\Panther\Unattend.xml`
		- ![[Pasted image 20250418172401.png]]
	- Decode using `base64`:
		- ![[Pasted image 20250418172422.png]]

