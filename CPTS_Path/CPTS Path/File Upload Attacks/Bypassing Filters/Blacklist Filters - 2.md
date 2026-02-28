## Fuzzing Extensions
----
- `PayloadsAllTheThings` provides lists of **extensions for [PHP](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Upload%20Insecure%20Files/Extension%20PHP/extensions.lst) and [.NET](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Upload%20Insecure%20Files/Extension%20ASP) web applications**. We may also use `SecLists` list of common [Web Extensions](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt).
- As we are testing a PHP application, **we will download and use the above [PHP](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Upload%20Insecure%20Files/Extension%20PHP/extensions.lst) list.**
- Then, from **`Burp History`, we can locate our last request to `/upload.php`**, right-click on it, and select **`Send to Intruder`**. From the `Positions` tab, we can `Clear` any automatically set positions, and then **select the `.php` extension in `filename="HTB.php"`** and click the `Add` button to add it as a fuzzing position:
	- ![[Pasted image 20260301010240.png]]
- Finally, we can `Load` the PHP extensions list from above in the `Payloads` tab under `Payload Options`. **We will also un-tick the `URL Encoding` option to avoid encoding the (`.`) before the file extension.** 
- We can sort the results by `Length`, and we will see that all **==requests with the Content-Length (`193`) passed the extension validation,==** as they all r**esponded with `File successfully uploaded`**. In contrast, the rest responded with an error message saying `Extension not allowed`.

# Non-Blacklisted Extensions
---
- Now, we can try uploading a file using any of the `allowed extensions` from above, and some of them may allow us to execute PHP code. 
- ==**`Not all extensions will work with all web server configurations`*==*, so we may need to try several extensions to get one that successfully executes PHP code.
- Let's use the `.phtml` extension, which PHP web servers often allow for code execution rights.
	- ![[Pasted image 20260301010438.png]]\

# Assignment
----
- `.phar` worked here:
```
┌──(drasstrange㉿kali)-[~/CPTS_Path]
└─$ curl 'http://154.57.164.67:30385/profile_images/shell1.phar?cmd=id'
uid=33(www-data) gid=33(www-data) groups=33(www-data)
                                                        
┌──(drasstrange㉿kali)-[~/CPTS_Path]
└─$ curl 'http://154.57.164.67:30385/profile_images/shell1.phar?cmd=cat+/flag.txt'
HTB{1_c4n_n3v3r_b3_bl4ckl1573d}
```