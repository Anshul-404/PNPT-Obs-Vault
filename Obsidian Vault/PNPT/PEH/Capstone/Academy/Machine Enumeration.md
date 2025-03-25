- After initial foothold in machine, we are `www-data`:
	- ![[Pasted image 20250325055452.png]]
- While searching, I found user `grimmie` in `/home` directory
- We are able to `cd` into that folder and view files (misconfigured permissions):
	- ![[Pasted image 20250325055634.png]]
# backup.sh (grimmie user)
---
- Bash script with following content:
- ```$ cat backup.sh
#!/bin/bash
rm /tmp/backup.zip
zip -r /tmp/backup.zip /var/www/html/academy/includes
chmod 700 /tmp/backup.zip

- This script is taking a backup of all the files in web directory `/var/www/html/academy/includes` in a `zip` file and storing in `/tmp/backup.zip`
- Then it is changing its perms, to `700`, meaning user can perform all operations
- ==However, this doesn't seem interesting as of now==

# /var/www/html/academy/db file
---
- Database file in `webpage` -> `onlinecourse.sql`
	- ![[Pasted image 20250325061050.png]]
	- Found possible admin credentials:
		- `admin  : 21232f297a57a5a743894a0e4a801fc3`
		- Cracking with hashcat:
			- ![[Pasted image 20250325061159.png]]
		- ==`admin  : 21232f297a57a5a743894a0e4a801fc3 : admin`==

# Database credentials
---
- `grep -iRl password` to find files containing `password` as string
![[Pasted image 20250325064256.png]]
- `grimmie : My_V3ryS3cur3_P4ss`