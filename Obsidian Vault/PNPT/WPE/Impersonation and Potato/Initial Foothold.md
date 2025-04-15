# Port 80
---
- Ask Jeeves
	- ![[Pasted image 20250415143125.png]]
- Searching for anything **loads this error image (not page)**:
	- ![[Pasted image 20250415143303.png]]
- Dirbusting and normal attacks result nothing

# Port 135, 445 - SMB
---
- No anonymous access found:
	- ![[Pasted image 20250415143643.png]]

# Port 50000 - Jetty 9.4.z-SNAPSHOT
---
- From nmap results:
	```
	50000/tcp open  http         Jetty 9.4.z-SNAPSHOT
	|_http-server-header: Jetty(9.4.z-SNAPSHOT)
	|_http-title: Error 404 Not Found
	```
- Gobuster Scan Results (after 10 minutes of scanning):
	- `gobuster dir -u http://10.10.10.63:50000/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt  -n -x php,txt,zip -t 50`
	- `/askjeeves`directory
	- ![[Pasted image 20250415150030.png]]
	- ![[Pasted image 20250415150049.png]]

# Jenkins Reverse Shell
---
- Directory: `http://10.10.10.63:50000/askjeeves/script`
	- ![[Pasted image 20250415150149.png]]
- https://blog.pentesteracademy.com/abusing-jenkins-groovy-script-console-to-get-shell-98b951fa64a6?gi=1563b0d08c14
- [[Groovy Reverse Shell]]
- ![[Pasted image 20250415150554.png]]
- `nc -lnvp 9001`
	- ![[Pasted image 20250415150616.png]]