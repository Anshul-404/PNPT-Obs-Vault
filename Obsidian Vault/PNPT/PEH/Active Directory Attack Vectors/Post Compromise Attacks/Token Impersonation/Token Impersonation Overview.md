# What are tokens?
---
- Temporary ==**keys that allow you access to a system/network** **without having to provide credentials**== each time. Think Tokens as ==cookies for computers.== 
- Types:
	- **Delegate**: Created for **logging into a** **machine or using RDP**.
	- **Impersonate**: **"non-interactive"** such as attaching **network drive or domain logon script**.

# Delegate Tokens
---
![[Pasted image 20250330033233.png]]

Why this is bad?
---
- Using metasploit's `incognito` module:
	- ![[Pasted image 20250330032154.png]]
	- ![[Pasted image 20250330032242.png]]


# Better Example on how this would be more useful
---
- Let's say we `impersonated` **==a logged in `domain admin`==**: `MARVEL/administrator`
- Then we can **==add another domain admin==** for persistent access:
	- ![[Pasted image 20250330032631.png]]
- Now we can **run `secretsdump.py`**. This previously was not possible as we did ==**not had password or even hash of the impersonated domain admin**==:
	- ![[Pasted image 20250330032753.png]]