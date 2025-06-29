# Password spraying
---
- Password spraying is a type of brute-force attack in which an attacker attempts to use a **single password across many different user accounts.**
- For example, if it is known that **administrators at a particular company commonly use `ChangeMe123!`** when setting up new accounts, it would be worthwhile to **spray this password across all user accounts** to identify any that were not updated.
	- `netexec smb 10.100.38.0/24 -u <usernames.list> -p 'ChangeMe123!'`

# Credential stuffing
---
- Credential stuffing is another type of brute-force attack in which an attacker uses **stolen credentials from one service to attempt access on others.**
- For example, if we have a **list of `username:password` credentials** obtained from a database leak, we can use `hydra` to **perform a credential stuffing attack** against an SSH service:
	- `hydra -C user_pass.list ssh://10.100.38.23`

# Default credentials
---
- Many systems—such as routers, firewalls, and databases—**come with `default credentials`.**
- While best practice dictates that administrators change these credentials during setup, **they are sometimes left unchanged, posing a serious security risk.**
- https://github.com/ihebski/DefaultCreds-cheat-sheet
	- `pip3 install defaultcreds-cheat-sheet`
	- `creds search linksys`
- In addition to publicly available lists and tools, **default credentials can often be found in product documentations.**
- Beyond applications, default credentials are also commonly associated with routers. One such list is available [here](https://www.softwaretestinghelp.com/default-router-username-and-password-list/).