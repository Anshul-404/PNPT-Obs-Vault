- We have `clean.sh` in `tmp` directory:
- ![[Pasted image 20250503150611.png]]

# Exploitation
---
- We can assume `clean.sh`is executed as some other user or use `pspy64` to view processes
	- ![[Pasted image 20250503150715.png]]
- Modify the script to get a bash reverse shell:
	- ![[Pasted image 20250503150740.png]]
	- ![[Pasted image 20250503150757.png]]