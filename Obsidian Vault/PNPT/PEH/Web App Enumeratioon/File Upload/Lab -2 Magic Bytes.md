![[Pasted image 20250407135303.png]]

# Information
- Let's try previous method by renaming it in `burpsuite`:
	- ![[Pasted image 20250407135626.png]]
- **This means there is some kind of server side processing**


# Magic Bytes
---
- **Magic Bytes of `png`** in hex are `89 50 4E 47 0D 0A 1A 0A`
- **Magic Bytes of `jpg`** in hex are `FF D8 FF E0`
- Or ==we can **just use `burpsuite`** to upload normal `png` file and then **replace `png` contents with `php` contents** **(except for Magic Bytes `PNG IDHR`)**==
- Also, **rename file to `shell.php`** as there is **also previous`client` side check** 
- Intercept in `burpsuite`
	- ![[Pasted image 20250407141020.png]]
- Add the `shell.php` code after the `IDHR` line:
	- ![[Pasted image 20250407141254.png]]
- NOTE :- this worked in video, but not for me (maybe as he used a simple command shell). I will try the `hexeditor` method
- Used `jpg` magic bytes 
- ![[Pasted image 20250407142555.png]]
- ![[Pasted image 20250407142522.png]]
- `http://localhost/labs/uploads/shell_modified_2.php`
- ![[Pasted image 20250407142719.png]]