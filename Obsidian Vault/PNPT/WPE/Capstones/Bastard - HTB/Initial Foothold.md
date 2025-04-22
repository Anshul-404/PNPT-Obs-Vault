[[PNPT/WPE/Capstones/Bastard - HTB/Enumeration|Enumeration]]

# Exploitation
---
- Ran using:
	- `ruby exploit.rb http://10.10.10.9`
- ![[Pasted image 20250419170830.png]]
- **We are not able to `cd`outside current directory**
# Upgrade to meterpreter using web delivery
---
- `use multi/script/web_delivery`
- ![[Pasted image 20250419171228.png]]

Note:- **Though we are `NT AUTHORITY`, we are not able to `cd` into `Administrator`folder** because
	It is a **low-privileged built-in IIS account** (Internet Information Services).