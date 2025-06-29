With **administrative access** to a Windows system, we can attempt to quickly **dump the files associated with the SAM database**, transfer them to our attack host, and begin cracking the hashes offline.

# Registry hives
---
- There are **three registry hives we can copy** if we have local administrative access to a target system. 
- A brief description of each is provided in the table below:
	![[Pasted image 20250629080847.png]]

## Using reg.exe to copy registry hives
---
- By launching **`cmd.exe` with administrative privileges,** we can use `reg.exe` to save copies of the registry hives:
	- `reg.exe save hklm\sam C:\sam.save`
	- `reg.exe save hklm\system C:\system.save`
	- `reg.exe save hklm\security C:\security.save`
- If we're **only interested in dumping the hashes** of local users, we need **only `HKLM\SAM` and `HKLM\SYSTEM`.** 
- However, it's often useful to **save `HKLM\SECURITY` as well**, since it can contain **cached domain user credentials on domain-joined systems, along with other valuable data.**