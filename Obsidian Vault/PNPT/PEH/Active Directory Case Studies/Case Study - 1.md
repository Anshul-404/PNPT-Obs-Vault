
- https://tcm-sec.com/pentest-tales-001-you-spent-how-much-on-security
- ![[Pasted image 20250401135716.png]]

- **Things we cannot do ❌ :**
---
- Cannot do **IPv6 attack** (disabled)
- **Responder** might not work as CyberArk (auto strong password rotation) as hashes might not be crackable.
- **OS or other services patched**

 - **What to do?** ✅
---
 - LLMNR?
 - We cannot crack the password, but we can **relay them**
 - **SMB Relay attack**
	 ![[Pasted image 20250401140205.png]]

- **Cracking the hashes**
---
- A lot of **same hashes were found**, so cracking can give high value:
	- Cracked Hash: `Power10`
- Password ==**reuse is prominent** even with **different accounts**.==

- **Moving Laterally**
---
- Moving laterally with all the **credentials on other machines** and dumping those hashes using `secretsdump` with found hashes or cracked hash.

# Conclusion
---
![[Pasted image 20250401140949.png]]