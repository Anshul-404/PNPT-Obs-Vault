- Pay **attention to `content-length` or `search` in response** (in burp or browser).
	- This changes based on response.
- Trying basic things didn't work, therefore bootup `burpsuite`
- **==Make sure to look at everything that our client sends or browser requests (could be session ids, some plugin, cookies, etc.)==**


# Burpsuite
---
- Login with given creds `jeremy:jeremy`
- Forward first `post` request
- For valid data:
	- ![[Pasted image 20250405165938.png]]
- For invalid data:
	- ![[Pasted image 20250405170133.png]]

 - **Let's try basic injection**
 ---
 - **Works** :
	 - ![[Pasted image 20250405170314.png]]
- **Good idea to url encode payload (Ctrl + U) as well, but here it works without it.**

# Extracting Data
- Number of columns being selected:
	- `somedata' UNION SELECT NULL,NULL,NULL,NULL#` => 4
	- ![[Pasted image 20250405170514.png]]