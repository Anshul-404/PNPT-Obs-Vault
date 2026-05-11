## Scenario

You are performing a web application penetration test for a software development company, and they task you with testing the latest build of their social networking web application. Try to utilize the various techniques you learned in this module to identify and exploit multiple vulnerabilities found in the web application.

The login details are provided in the question below.


Q1> Try to escalate your privileges and exploit different vulnerabilities to read the flag at '/flag.php'
Authenticate to 154.57.164.79 , with user "`htb-student`" and password "`Academy_student!`"


- Run bruteforce script to find:
	- {"uid":"52","username":"a.corrales","full_name":"Amor Corrales","company":"Administrator"}

- Reset Password in settings and capture requests to **==find `token` api==**:
	- Find token of admin `52`:
	- `e51a85fa-17ac-11ec-8e51-e78234eb7b0c`
	- ![[Pasted image 20260501011600.png]]
- Using this token with `POST` reset password API gives `Access Denied`, so **==convert it to GET request==**:
	- ![[Pasted image 20260501011825.png]]
- Login with admin user:
	- New option **`Add Event` is available for admin:**
	- ![[Pasted image 20260501011913.png]]
	- ![[Pasted image 20260501011934.png]]
- Capture request:
	- ![[Pasted image 20260501012022.png]]
	- Looks like **==XML, let's try XXE==**:
		- ![[Pasted image 20260501012112.png]]