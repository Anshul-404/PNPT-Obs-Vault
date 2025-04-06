- Got access to **linux server from where we can ping this ip** ([[Privilege Escalation]])

# Pivoting
---
- We already have our own user there, **let's pivot using `sshuttle`**:
	- `sshuttle -r drasstrange@10.200.107.33 10.200.107.0/24 -x 10.200.107.33`
	- `password: password123`
	- ![[Pasted image 20250405102126.png]]

