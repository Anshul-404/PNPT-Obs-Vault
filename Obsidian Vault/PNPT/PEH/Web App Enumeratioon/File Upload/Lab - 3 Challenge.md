- Trying previous `magic bytes` technique:
	- ![[Pasted image 20250407143553.png]]
- Does not seem to be working, there are additional checks
- From the sentence: `The file extension '.php' is not allowed.`, **looks like there is `deny` list** on the **server side.**

# Exploitation
---
- Extensions like:
	- ![[Pasted image 20250407143927.png]]
- **Can be used to bypass `php` blacklist**
- ==**Let's rename from `shell_modified_2.jpg` to `shell_modified_2.phtml` in `burpsuite`==**
	- ![[Pasted image 20250407144034.png]]
- `http://localhost/labs/uploads/shell_modified_2.phtml`
- ![[Pasted image 20250407144117.png]]