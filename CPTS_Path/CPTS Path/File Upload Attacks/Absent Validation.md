The most basic type of file upload vulnerability occurs when the **web application `does not have any form of validation filters` on the uploaded files**, allowing the **==upload of any file type by default.==**

# Arbitrary File Upload
---
- Let's start the exercise at the end of this section, and we will see an `Employee File Manager` web application, which allows us to upload personal files to the web application:
	- ![[Pasted image 20251107000344.png]]

- The web application **==does not mention anything about what file types are allowed==**, and we can drag and drop any file we want, and its name will appear on the upload form, **==including `.php` files==**:
	- ![[Pasted image 20251107000556.png]]

# Identifying Web Framework
---
- A **==Web Shell provides us with an easy method to interact with the back-end server by accepting shell commands==** and printing their output back to us within the web browser.
- A web shell has to be **==written in the same programming language that runs the web server==**, as it runs platform-specific functions and commands to execute system commands on the back-end server.

- One easy method to determine what language runs the web application is to **==visit the `/index.ext` page, where we would swap out `ext` with various common web extensions, like `php`, `asp`, `aspx`==**, among others, to see whether any of them exist.
- For example, when we visit our exercise below, we see its URL as `**http://SERVER_IP:PORT/`, as the `index` page is usually hidden** by default. **But, if we try visiting `http://SERVER_IP:PORT/index.php`, we would get the same page**, which means that t**his is indeed a `PHP` web application.**
- We do not need to do this manually, of course, as we can use a tool like Burp Intruder for fuzzing the **file extension using a [Web Extensions](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt) wordlist.**
- This method may not always be accurate, though, as the web application **may not utilize index pages or may utilize more than one web extension.**

- Several other techniques may help identify the technologies running the web application, **==like using the [Wappalyzer](https://www.wappalyzer.com) extension, which is available for all major browsers==**.

# Vulnerability Identification
---
- Now that we have identified the web framework running the web application and its programming language, we can **test whether we can upload a file with the same extension**.
- As an initial test to identify whethe**r we can upload arbitrary `PHP` files, let's create a basic `Hello World` script to test** whether we can **execute `PHP` code with our uploaded file.**
	- `echo '<?php echo "Hello HTB";?>' > test.php`
	- ![[Pasted image 20251107001245.png]]

- Now, we can **click the `Download` button, and the web application will take us to our uploaded file**:
	- ![[Pasted image 20251107001255.png]]
- If the page **==could not run PHP code, we would see our source code printed on the page.==**