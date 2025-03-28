- Save the hash in a file like `hashes.txt`
```

```
- Crack using **hashcat**:
	- Get help or from `hashcat` modules page to search for hashes
![[Pasted image 20250328142704.png]]
- `hashcat -m 5600 hashes.txt /usr/share/wordlists/rockyou.txt`
- Cracked in a second:
![[Pasted image 20250328143333.png]]

# Hashcat Tips:
---
- If already cracked previously then use:
	- `hashcat -m 5600 hashes.txt /usr/share/wordlists/rockyou.txt --show`
- Use `-O` option to optimisel
- Better wordlists like **rockyou2021.txt** exists (91 GBs)
- Using `oneruletorulethemall` rule, it mutates password list
	- ![[Pasted image 20250328143804.png]]
- Trying password related to sports like pittsburghsteelers or `pittsburghpirates`, company names, etc to add rules.