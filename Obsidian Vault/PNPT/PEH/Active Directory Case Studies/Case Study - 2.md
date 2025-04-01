https://tcm-sec.com/pentest-tales-002-digging-deep/
![[Pasted image 20250401141029.png]]

- **Things we cannot do ❌ :**
---
- Cannot do **IPv6 attack** (disabled)
- Cannot do SMB relay **SMB Signin enabled**
- **Patched**


 - **What to do?** ✅:
---
 - Try searching **==what is available on the network (like webpages on server).==**
	 - Servers, printers, accounts, etc
	 - **For example, if I find IDRAC server, try with default credentials.** (root:calvin)
- A security **tool in development** found with **password in clear text**:
	- ![[Pasted image 20250401141431.png]]

 - **Sweeping the network with credentials (administrator : foundPassword)**
---
- ![[Pasted image 20250401141545.png]]

- **Dump them hashes (secretsdump)**
---
- ![[Pasted image 20250401141640.png]]
- **==Pay attention to `wdigest` if on previous Operating Systems (especially servers)==**
	- ![[Pasted image 20250401141807.png]]
- Found a service's clear text password using `wdigest`:
	- ![[Pasted image 20250401141923.png]]
	- **This service account was `domain admin`**

# Conclusion
---
![[Pasted image 20250401142117.png]]