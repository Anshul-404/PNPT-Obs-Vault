# Port 31331 - HTTP
---
![[Pasted image 20250501160548.png]]
- `gobuster dir -u http://10.10.163.183:31331/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt  -n -x zip,txt -t 100`
	- `robots.txt`
		- ![[Pasted image 20250501160609.png]]
	- ![[Pasted image 20250501160621.png]]
- Login page on `/partners.html`:
	- ![[Pasted image 20250501160646.png]]


# Port 8081 - APIs
---
- From gobuster scan:
	- `gobuster dir -u http://10.10.163.183:8081/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt  -n -x zip,txt -t 100`
	- ![[Pasted image 20250501162218.png]]

- ![[Pasted image 20250501162304.png]]
- Ping utility might accept `ip`as an argument -> **Rough Guess**
	- Ping our IP:
	- ![[Pasted image 20250501162343.png]]
