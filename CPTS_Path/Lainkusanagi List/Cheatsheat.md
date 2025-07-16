# Enumeration
---
## Discovering Ports / Information
- Ping machine to Get info:
	- If `rrt` is similar to 128, likely windows
- Start nmap scan on common ports with scripts and services. This can **take time on Windows**:
	-  `nmap -sV -sC 10.10.10.95 -Pn -T4 10.10.10.95 -oN nmap/nmap_common`
- Run `rustscan` to discover open ports quickly:
	- `rustscan -a 10.10.10.95 | tee nmap/rustscan`
## Vulnerabilities
- Search for discovered service Version on google:
	- `Service Name + Version + Exploit`
	- `Service Name + Version + Exploit + Github POC`
- Using searchsploit: `searchsploit name version`
- On msfconsole: `search service version`

## Default Credentials
- Search Default Creds: `creds search tomcat`
- Apache Tomcat : `admin`: `s3cret`

## Directory Busting
- Bruteforce common directories and extensions first:
	- `gobuster dir -u http://10.129.203.7/ -w /usr/share/wordlists/dirb/common.txt  -t 100 -x txt,zip`
- Gobuster again with bigger wordlist:
	- ` gobuster dir -u http://10.10.10.95/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt  -t 100 -x txt,zip,php`

# Services Interaction
- FTP/20-21:
	- We can login as **anonymous so ALWAYS try that**:
		- `ftp anonymous@10.10.10.10`-> `password: anonymous`
	- Sometimes, we can also **login as users without any password, so try that too** if some user name is known