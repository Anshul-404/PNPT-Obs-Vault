![[Pasted image 20250407153248.png]]
- **Challenge**:- Find a valid account and login

# Checklist from `appsecexplained`:
---
![[Pasted image 20250407154044.png]]

# Exploitation
---
- Was **not able to solve myself**
- Key point:
	- Since we have 5 attempts, **make a password list of 4 passwords**
	- But, **we can make list of any number of users**
	- ==Passwords hints: top 10 from `rockyou`, `xato`, name of the webapp (teashop), thecybermentor, etc.==
- `ffuf -request request_lab3.txt -mode clusterbomb -request-proto http -w usernames.txt:FUZZ -w passwords.txt:FUZ2"`
	- ![[Pasted image 20250407162527.png]]
	- ![[Pasted image 20250407162542.png]]