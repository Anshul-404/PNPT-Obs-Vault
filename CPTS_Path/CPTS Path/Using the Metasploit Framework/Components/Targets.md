- Targets are unique **operating system identifiers** taken from the versions of those specific operating systems which adapt the **selected exploit module to run on that particular version of the operating system.** 

# MSF - Show Targets
---
- After selecting a module:
	- `show targets`
	- ![[Pasted image 20250627073512.png]]

# Selecting a Target
---
- If we want to find out more about a specific module and what the vulnerability behind it does, **we can use the `info` command.**
- This command can help us out whenever we are unsure about the origins or functionality of different exploits or auxiliary modules.
- Looking at the description, **we can get a general idea of what this exploit will accomplish for us**. 
- Keeping this in mind, **==we would next want to check which versions are vulnerable==** to this exploit.
- If we know what versions are running on our target, **we can use the 
	- `set target <index no.>` command to pick a target from the list.
- Otherwise, metasploit will select it automatically after performing a detection.

# Target Types
---- 
- Every target can vary from another by service pack, OS version, and even language version.
- The return address can vary because a **particular language pack changes addresses, a different software version is available, or the addresses are shifted due to hooks**.
- This address can be `jmp esp`, a jump to a specific register that identifies the target, or a `pop/pop/ret`.

- To identify a target correctly, we will need to:
	- Obtain a copy of the target binaries
	- Use msfpescan to locate a suitable return address