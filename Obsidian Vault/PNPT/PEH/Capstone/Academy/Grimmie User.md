- Password found with mysql `config` file [[Machine Enumeration]]
- `grimmie : My_V3ryS3cur3_P4ss`
![[Pasted image 20250325064415.png]]

# SSH into grimmie
---
- **backup.sh** in user directory
- Bash script with following content:
- ```$ cat backup.sh
	#!/bin/bash
	rm /tmp/backup.zip
	zip -r /tmp/backup.zip /var/www/html/academy/includes
	chmod 700 /tmp/backup.zip

- This script is taking a backup of all the files in web directory `/var/www/html/academy/includes` in a `zip` file and storing in `/tmp/backup.zip`
- Then it is changing its perms, to `700`, meaning user can perform all operations
- ![[Pasted image 20250325064843.png]]
- Seems like the file is created by `root` user. This would mean, that there is probably a cronjob running `backup.sh` script by `root` user.
