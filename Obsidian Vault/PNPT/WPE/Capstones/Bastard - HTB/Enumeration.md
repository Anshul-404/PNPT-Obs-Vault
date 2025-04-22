# Port 80
---
![[Pasted image 20250419170450.png]]
- Default credentials and creating new account did not work

- **Nikto**
---
- Nikto revealed a file to check the version:
	- `CHANGELOG.txt`
	- ![[Pasted image 20250419170602.png]]
- Searching this, revealed the exploit https://www.exploit-db.com/exploits/44449: **Drupalggedon2**
	- Ruby exploit
	- ![[Pasted image 20250419170657.png]]
- Run using:
	- `ruby exploit.rb http://10.10.10.9`
		- Missing dependencies install: `sudo gem install highline`
