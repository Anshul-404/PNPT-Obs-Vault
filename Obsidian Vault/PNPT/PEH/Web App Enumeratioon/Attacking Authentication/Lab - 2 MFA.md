Things to think about:
- **Brute force the pin / Bruteforce the password**
- **Change from backend**
- **Weak tokens / Single token valid for all**

# Exploitation
---
![[Pasted image 20250407152706.png]]
- After generating the MFA, we're not able to change the `Username` field
- Copy `jessamy`'s `MFA` code
	- ![[Pasted image 20250407152754.png]]
- **We can now either use `burpsuite` or `edit and resend` request from `firefox`**
- ==**Note:- Firefox method might not work always as code might expire after first use, better to modify very first request using burp==**
- Firstly, login as `jessamy` to get the original `request`
- ![[Pasted image 20250407152935.png]]
- Then `edit` the username to `jeremy` and resend
	- ![[Pasted image 20250407153006.png]]
	- ![[Pasted image 20250407153019.png]]
