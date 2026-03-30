# Nmap Scan
---
```
└─$ nmap -sV -sC 192.168.154.47 -p- -oN nmap/nmap_all -T4
Starting Nmap 7.95 ( https://nmap.org ) at 2026-03-28 14:49 IST
Nmap scan report for 192.168.154.47
Host is up (0.070s latency).
Not shown: 65529 filtered tcp ports (no-response)
PORT     STATE  SERVICE      VERSION
21/tcp   open   ftp          vsftpd 3.0.3
22/tcp   open   ssh          OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 10:62:1f:f5:22:de:29:d4:24:96:a7:66:c3:64:b7:10 (RSA)
|   256 c9:15:ff:cd:f3:97:ec:39:13:16:48:38:c5:58:d7:5f (ECDSA)
|_  256 90:7c:a3:44:73:b4:b4:4c:e3:9c:71:d1:87:ba:ca:7b (ED25519)
80/tcp   open   http         Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Enter a title, displayed at the top of the window.
139/tcp  closed netbios-ssn
445/tcp  closed microsoft-ds
5437/tcp open   postgresql   PostgreSQL DB 11.3 - 11.9
| ssl-cert: Subject: commonName=debian
| Subject Alternative Name: DNS:debian
| Not valid before: 2020-04-27T15:41:47
|_Not valid after:  2030-04-25T15:41:47
|_ssl-date: TLS randomness does not represent time
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 115.43 seconds
```

- After searching for everything for FTP, HTTP and SMB, I found nothing.
- While searching for **PostgreSQL DB 11.3 - 11.9**, I found a potential vulnerability:
	- `https://www.exploit-db.com/exploits/50847`

# Postgresql Vulnerability:
---
- In PostgreSQL 9.3 through 11.2, **the "COPY TO/FROM PROGRAM" function** allows superusers and users in the 'pg_execute_server_program' group to **==execute arbitrary code in the context of the database's operating system user==**. This functionality is enabled by default and can be abused to run arbitrary operating system commands on Windows, Linux, and macOS.

- Manual exploitation:
	- ![[Pasted image 20260328161222.png]]
- We can also use this script:
	- `https://www.exploit-db.com/exploits/50847`
	- ![[Pasted image 20260328161257.png]]

# Upgrade shell
----
- I was not able to get a pretty shell, so **==modified the python script to act as a shell==** [[postgres_advanced.py]]

# Privilege Escalation 
---
- There was no way to get good output of linpeas, so with manual enum, I found SUID bits:
	- ![[Pasted image 20260328171445.png]]
- `find` binary can run with ==**SUID bits**==.
	- ==**Note:- `|| true` is used to ALWAYS return 0 status code, otherwise the script fails==**

- Just find the root flag and read it:
	- `find /root/ || true`
	- `find /root/proof.txt -exec cat {} \;`
		- ![[Pasted image 20260328171710.png]]