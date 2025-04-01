- Nothing was working even when they were capturing hashes.
- **MITM was possible with IPv6**, but less `domain admin` accounts, so not getting those hashes as don't know when they'll login.
- No default or weak credentials were found on web services.
# Unique Path
---
- Some captured **hashes were crackable** and got access to 1 account (not domain admin) as password policy was not great: ![[Pasted image 20250401142756.png]]

- Found this user with **access to everybody's file shares:**
	- ![[Pasted image 20250401142948.png]]
- In `New Mackbook Pro Setup Procedure` **document, we found some credentials:**
	- ![[Pasted image 20250401143120.png]]

- **Let's spray this password using `crackmapexec`:**
---
- Got access to **another machine**:
	![[Pasted image 20250401143211.png]]


- **Dump them hashes (`secretsdump`):**
---
- `notservices` `domain admin` **==account found with clear text password from cache:==**
- ![[Pasted image 20250401143322.png]]

- **==Everytime you get credentials, new information, account access, YOU ENUMERATE!!!!!==**