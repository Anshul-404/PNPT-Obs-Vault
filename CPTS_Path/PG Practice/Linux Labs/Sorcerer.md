# Nmap Scan
-----
```
PORT      STATE SERVICE  VERSION
22/tcp    open  ssh      OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 81:2a:42:24:b5:90:a1:ce:9b:ac:e7:4e:1d:6d:b4:c6 (RSA)
|   256 d0:73:2a:05:52:7f:89:09:37:76:e3:56:c8:ab:20:99 (ECDSA)
|_  256 3a:2d:de:33:b0:1e:f2:35:0f:8d:c8:d7:8f:f9:e0:0e (ED25519)
80/tcp    open  http     nginx
|_http-title: Site doesn't have a title (text/html).
111/tcp   open  rpcbind  2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100003  3           2049/udp   nfs
|   100003  3,4         2049/tcp   nfs
|   100005  1,2,3      42329/tcp   mountd
|   100005  1,2,3      59704/udp   mountd
|   100021  1,3,4      33065/tcp   nlockmgr
|   100021  1,3,4      51595/udp   nlockmgr
|   100227  3           2049/tcp   nfs_acl
|_  100227  3           2049/udp   nfs_acl
2049/tcp  open  nfs      3-4 (RPC #100003)
7742/tcp  open  http     nginx
|_http-title: SORCERER
8080/tcp  open  http     Apache Tomcat 7.0.4
|_http-favicon: Apache Tomcat
|_http-title: Apache Tomcat/7.0.4
33065/tcp open  nlockmgr 1-4 (RPC #100021)
35835/tcp open  mountd   1-3 (RPC #100005)
42329/tcp open  mountd   1-3 (RPC #100005)
43307/tcp open  mountd   1-3 (RPC #100005)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 873.05 seconds
```

- Most of these gave nothing

# Port 7742
----
- When we open it, we get a login page:
	- ![[Pasted image 20260411141203.png]]
- But we can see, clicking login does nothing, no network requests are made:
	- ![[Pasted image 20260411141255.png]]
- We run **gobuster scan** and find **zipfiles**:
	- ![[Pasted image 20260411141327.png]]
- ![[Pasted image 20260411141358.png]]
- Download and extract these:
	- Only **==max looks interesting==** (scp_wrapper_2.sh is my file that I created during testing):
	- ![[Pasted image 20260411141516.png]]
# Initial Foothold
---
- As we can see, when we try doing ssh, it **==forces scp==**:
	- ![[Pasted image 20260411141655.png]]
	- Here `command` means the **==command which will get executed once we do successful authentication with ssh.==**
	- ![[Pasted image 20260411141758.png]]

- We also have **`tomcat-users.xml.bak` with non-default password which was a rabbit hole** as no port allowed direct tomcat authentication from **manager panel**:
	- ![[Pasted image 20260411141849.png]]

- We can utilize the **==scp functionality==** to simple **==replace `scp_wrapper.sh` script with `bash` or `sh`==**:
	- ![[Pasted image 20260411142046.png]]
- It looks like it failed, but **it works**:
	- ![[Pasted image 20260411142125.png]]
- We can get a stable shell using python and upload linpeas.sh for more enumeration

# Privilege Escalation
---
- We **==find `SUID` for `start-stop-daemon`:==**
	- ![[Pasted image 20260411142254.png]]
- From GTFOBINS:
	- ![[Pasted image 20260411142322.png]]
- ![[Pasted image 20260411142348.png]