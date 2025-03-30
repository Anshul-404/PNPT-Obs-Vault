- Login on `Spiderman` as local user `pparker` (not domain user).
- Get the `mimikatz` files uploaded on AD machine:
	- `mimidrv.sys`
	- `mimikatz.exe`
	- `mimilib.dll`
	- `mimispool.dll`
- ![[Pasted image 20250330065822.png]]

- Start `cmd` as `Administrator` and `cd` into the `mimikatz` files directory
- Now run:
	- `mimikatz.exe`
	- `privilege::debug` => debug privilege (perm all the attacks we want)
	- `sekurlsa::` => to see possible attacks
- ![[Pasted image 20250330142459.png]]

# Something Different than `secretsdump` and `lsassy`
---
- Note: `mimikatz` did not work on my machine, so taking notes from the videos.
- **==Clear text password==** for `Administrator` (**==`kamran`==** on our AD lab) found:
	![[Pasted image 20250330144719.png]]
	- ==**NTLM hash** of `kamran`==
	![[Pasted image 20250330145016.png]]

- Why?
	- Because when setting up **==`hackme` share,==** we used the **==`Connect using different credentials`==** option:
		- ![[Pasted image 20250330144830.png]]