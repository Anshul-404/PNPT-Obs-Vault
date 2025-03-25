- From nmap scan:
	- ```80/tcp open  http    Apache httpd 2.4.38 ((Debian))
	  |_http-title: Apache2 Debian Default Page: It works```
- Default page:
- ![[Pasted image 20250325051456.png]]

**Gobuster scanning:**
---
- `gobuster dir -u http://192.168.157.130/  -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt  -n -x php,html,txt,zip -t 100`
![[Pasted image 20250325053746.png]]

- **http://192.168.157.130/academy/ Directory**:
---
![[Pasted image 20250325053853.png]]
- Trying credentials obtained from FTP (note.txt) [[Port 22 (FTP)]]
	- `10201321 : cd73502828457d15655bbd7a63fb0bc8 : student`

# Gaining Initial Foothold
---
- Portal let's us upload photo, even php files as images.
- This uploaded file opens when we view our profile.
 ![[Pasted image 20250325055137.png]]
- We utilize it to run a php reverse shell from pentest monkey.

- **Gaining shell**
---
   - ![[Pasted image 20250325055239.png]]
   - Listen on port 9001:
	   - `nc -lnvp 9001`
   - Refresh the page to gain shell:
	   - ![[Pasted image 20250325055346.png]]

# Admin login page
---
- Found admin directory after gaining the shell
- ![[Pasted image 20250325062922.png]]
- http://192.168.157.130/academy/admin/index.php

![[Pasted image 20250325062841.png]]
