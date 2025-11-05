=> Assess the web application and use a variety of techniques to **gain remote code execution** and **find a flag in the / root directory** of the file system. 
Submit the **contents of the flag as your answer.**

- ![[Pasted image 20251105075324.png]]
- Bruteforcing `contact.php` for parameters:
	- `ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://83.136.249.223:37753/contact.php?FUZZ=value' -fs 1771`
	- ![[Pasted image 20251105075347.png]]
- But does not work. We also found `api/image.php` loading images:
	- ![[Pasted image 20251106010240.png]]
- Let's bruteforce this parameter (had to look at writeup as apparently, I missed the most important (this one) step):
	- `ffuf -w ~/Downloads/LFI-Jhaddix.txt:FUZZ -u 'http://83.136.249.223:45125/api/image.php?p=FUZZ' -fl 1`

- This works ONLY with curl, **not on browser as it interprets it as an image:**
	- ![[Pasted image 20251106010319.png]]

- Manual enumeration reveals base root directory as `/var/www/html`:
	- ![[Pasted image 20251106010601.png]]

# RCE
---
- Let's look at different `.php` files.
- `application.php`:
	- ![[Pasted image 20251106021658.png]]
	- This reveals that **uploaded files are renamed to md5 hash and then stored** in `uploads` folder
- `contact.php`:
	- ![[Pasted image 20251106021814.png]]
	- The first part **rejects the `region` parameter if it contains `.` or `/`**. Otherwise, it **url decodes it and stores in `region` variable.**
	- The second part **appends `./regions/` subdirectory at the start** of the url-decoded string **and `.php` at the end.** 

## Exploitation
- Let's upload a shell with a static command and get its md5 hash:
	- ![[Pasted image 20251106022045.png]]
- Upload it from `Apply`. The file should become `2c9ad1cf4f0065f54c6228edcb3bd0fe.php` in `uploads` directory:
	- ![[Pasted image 20251106022128.png]]
- Let's try to include it from `contact.php`.
- To do this, we need to go one step back using `../` to go out of the `regions` sub-directory.
	- After that, we goto `uploads` folder and access the generated php file.

- **Note:-** We need to **==double url-encode our payload==** because the first encoding is decoded by browser or php itself and then it checks for `.` and `/`. 
	- If we double encode it, **==the code approves the string as it is still encoding once more==**. This **==second encoding layer is decoded by `url_decode` function by php as we saw in `contact.php`==** file.

- Our payload becomes:
	- `curl http://83.136.249.223:30958/contact.php?region=%252E%252E%252Fuploads%252F2c9ad1cf4f0065f54c6228edcb3bd0fe`
	- **Removed `.php` from end as it is automatically appended** by `contact.php`.
- ![[Pasted image 20251106022628.png]]

- Read the flag using this `final.php` file:
	- ![[Pasted image 20251106022830.png]]
- Final payload:
	- `curl http://83.136.249.223:30958/contact.php?region=%252E%252E%252Fuploads%252Ff30ea82fc92768dcafa1a33b7f42db57`
	- ![[Pasted image 20251106022850.png]]