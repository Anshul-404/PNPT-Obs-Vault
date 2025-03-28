# Link Local Multitask Name Resolution / NBT-NS
---
- Used to identify hosts when DNS fails to do so.
- Key flaw : This service **==utilize a user's username and NTLMv2 hash==** when appropriately responded to

- Type of **MITM** attack
![[Pasted image 20250328042316.png]]

# Actual Attack:
---
- Good time to launch in the morning when people are l**ogging in and trying to access shares / resources on the network**.
- Responder Tool:
	- `sudo responder -I tun0 -dwP -v`
	- When it ==senses== this type of traffic, ==it will grab the **hash**==
	- We can **crack the hash** if it is weak