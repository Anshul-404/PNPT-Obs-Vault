# Extension Fuzzing
---
- One common way to identify that is by **finding the server type through the HTTP response headers and guessing the extension**. 
- For example, **==if the server is `apache`, then it may be `.php`, or if it was `IIS`, then it could be `.asp` or `.aspx`, and so on.==**
	- This method is **not very practical, though**
- Instead of placing the `FUZZ` keyword where the directory name would be, **==we would place it where the extension would be `.FUZZ`, and use a wordlist for common extensions==**. We can utilize the following wordlist in `SecLists` for extensions:
	- `/opt/useful/seclists/Discovery/Web-Content/web-extensions.txt`
- We **can always use two wordlists and have a unique keyword for each, and then do `FUZZ_1.FUZZ_2` to fuzz for both.** 
- However, there is one file we can **always find in most websites, ==which is `index.*`==, so we will use it as our file and fuzz extensions on it.**
	- `ffuf -w /opt/useful/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/blog/indexFUZZ`
	- **==Note==**: The **wordlist we chose already contains a dot (.)**, so we will not have to add the dot after "index" in our fuzzing.

# Page Fuzzing
---
- We will now use the same concept of keywords we've been using with `ffuf`, use `.php` as the extension, place our `FUZZ` keyword where the filename should be, and use the same wordlist we used for fuzzing directories:
	- `ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/blog/FUZZ.php`