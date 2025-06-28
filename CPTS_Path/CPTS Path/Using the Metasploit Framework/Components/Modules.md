- Metasploit `modules` are **prepared scripts with a specific purpose** and corresponding functions that have **already been developed and tested in the wild.**
- The `exploit` category consists of so-called **proof-of-concept (`POCs`)** that can be used to exploit existing vulnerabilities in a largely automated manner.
- Many people often think that the ==**failure of the exploit**== disproves the existence of the suspected vulnerability. However, this is only proof that the **==Metasploit exploit does not work and NOT that the vulnerability does not exist==**
- Therefore, **automated tools such as the Metasploit framework should ONLY be considered a support tool** and not a substitute for our manual skills.

# Syntax
---
![[Pasted image 20250627071714.png]]

# Type
---
- The `Type` tag is the first level of segregation between the Metasploit `modules`.
- Looking at this field, we can tell w**hat the piece of code for this module will accomplish.**
- ![[Pasted image 20250627071818.png]]

# Searching for Modules
---
- `help search`
	- ![[Pasted image 20250627071912.png]]
	- ![[Pasted image 20250627071923.png]]