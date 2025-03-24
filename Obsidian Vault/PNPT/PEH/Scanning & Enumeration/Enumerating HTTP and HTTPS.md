- `SSH 22` is not that useful (but always worth a try, especially with know usernames or credentials) as it does not have much known vulnerabilities.
- `SMB (139) / NFS` however, are historically bad, and prime targets.
- Same with `80/443`, websites are known to be vulnerable.
- We are looking for hidden directories, files, services, source code, version information, backend directories, information disclosure, vulnerabilities scanning tools, etc.
  
# HTTP / HTTPS port on Kioptrix:
---
- Found a default test page``
- **Are they hosting a website? Testing something? Left open for no reason?**
![[Pasted image 20250324134345.png]]
```0/tcp    open  http        Apache httpd 1.3.20 ((Unix)
|_http-title: Test Page for the Apache Web Server on Red Hat Linux
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) 
| http-methods: 
|_  Potentially risky methods: TRACE
```
- Information disclosure (Apache Server version):
  ![[Pasted image 20250324135823.png]]
  
  
- **Nikto Automated  Vulnerability Scanner**
---
- `nikto -h[ost] www.url.com`
	- `nikto -h http://192.168.157.128/`
	- Results : [[nikto for kioptrix]]

- **Directory Bruteforcing with Dirbuster / Gobuster**
---
- Look for interesting files, directories and status codes
- ![[Pasted image 20250324141202.png]]
- This reveals `usage_20090.html` file.
- ![[Pasted image 20250324142344.png]]
	- `Webalizer Version 2.01`: Vulnerable to multiple XSS (found through google, BO, etc.)
- Also reveiled [http://192.168.157.128/mrtg/forum.html]
	- Information Disclosure (Possible Users / Admins):
	-  ![[Pasted image 20250324142603.png]]

- **Burpsuite**
---
- Try sending different type of requests (GET, POST, HEAD, etc)

- **Viewing Source Code**
---
- Look for comments, keys, links, passwords, etc