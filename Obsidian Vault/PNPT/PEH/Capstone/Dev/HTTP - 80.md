```
80/tcp    open  http     Apache httpd 2.4.38 ((Debian))
|_http-title: Bolt - Installation error
|_http-server-header: Apache/2.4.38 (Debian)
```
- No known exploits found for `Apache/2.4.38`
![[Pasted image 20250325132200.png]]

# Gobuster
----
- `gobuster dir -u http://192.168.157.131/  -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt  -n -x php,html,txt,zip -t 100`
- ![[Pasted image 20250325132646.png]]

- **App Directory -> config**
---
- ![[Pasted image 20250325132742.png]]
- -> `config.yml` file
- ![[Pasted image 20250325132944.png]]
- I_love_java