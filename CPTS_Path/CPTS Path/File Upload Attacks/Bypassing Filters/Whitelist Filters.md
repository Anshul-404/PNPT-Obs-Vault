- Whitelist is generally **more secure than a blacklist**. The web server **would only allow the specified extensions**, and the list would not need to be comprehensive in covering uncommon extensions.
- A blacklist may be helpful in cases where the upload functionality needs to allow a wide variety of file types (e.g., File Manager), while a whitelist is usually only used with upload functionalities where only a few file types are allowed.

## Whitelisting Extensions
---
- We see that we get a **message saying `Only images are allowed`, which may be more common in web apps** than seeing a blocked extension type.
- However, error messages d**o not always reflect which form of validation** is being utilized, so let's try to **fuzz for allowed extensions** as we did in the previous section:
	- ![[Pasted image 20260301021048.png]]
- We can see that **==all variations of PHP extensions are blocked (e.g. `php5`, `php7`, `phtml`)==**. 
- However, the wordlist we used also contained **==other 'malicious' extensions that were not blocked and were successfully uploaded.==**
- The following is an example of a file extension whitelist test:
```php
	$fileName = basename($_FILES["uploadFile"]["name"]);
	
	if (!preg_match('^.*\.(jpg|jpeg|png|gif)', $fileName)) {
	    echo "Only images are allowed";
	    die();
	}
```
- We see that the script **uses a Regular Expression (`regex`) to test whether the filename contains any whitelisted image extensions.**
- The issue here lies within the `regex`, as it ==**only checks whether the file name `contains` the extension and not if it actually `ends` with it.**==

## Double Extensions
---
- For example, if the `.jpg` extension was allowed, we can add it in our uploaded file name and still end our f**ilename with `.php` (e.g. `shell.jpg.php`), in which case we should be able to pass the whitelist test**, while still uploading a PHP script that can execute PHP code.
	- ![[Pasted image 20260301021236.png]]

- However, **this may not always work,** as some web **==applications may use a strict `regex` pattern, as mentioned earlier==**, like the following:
	- `if (!preg_match('/^.*\.(jpg|jpeg|png|gif)$/', $fileName)) { ...SNIP... }`
- This pattern should only consider the final file extension, as it uses (`^.*\.`) to match everything up to the last (`.`), **==and then uses (`$`) at the end to only match extensions that end the file name==**

## Reverse Double Extension
---
- In some cases, the file upload functionality itself may not be vulnerable, **but the web server configuration may lead to a vulnerability.**
- For example, an organization may use an open-source web application, which has a file upload functionality.
- Even if the file upload functionality uses a strict regex pattern that only matches the final extension in the file name, **==the organization may use the insecure configurations for the web server.==**
- For example, the `/etc/apache2/mods-enabled/php7.4.conf` for the `Apache2` web server may include the following configuration:
	```xml
	<FilesMatch ".+\.ph(ar|p|tml)">
	    SetHandler application/x-httpd-php
	</FilesMatch>
```

- It specifies a **whitelist with a regex pattern that matches `.phar`, `.php`, and `.phtml`.** (in configuration, even though it is blocked from upload function)
- However, this regex pattern **==can have the same mistake we saw earlier if we forget to end it with (`$`).==**
- In such cases, **any file that contains the above extensions will be allowed PHP code execution,** **==even if it does not end with the PHP==** extension. **==For example, the file name (`shell.php.jpg`)==**
- All the a**bove allowed files by the configuration WILL be treated as php and WILL be executed.**

## Character Injection
---
- We can ==**inject several characters before or after the final extension**== to cause the web application to **misinterpret the filename and execute the uploaded file as a PHP script.**
- The following are some of the characters we may try injecting:
	- `%20`
	- `%0a`
	- `%00`
	- `%0d0a`
	- `/`
	- `.\`
	- `.`
	- `…`
	- `:`
- **==Each character has a specific use case that may trick the web application==** to misinterpret the file extension.
- For example, **(`shell.php%00.jpg`) works with PHP servers with version `5.X` or earlier,** as it causes the PHP web server to end the file name after the (`%00`), and store it as (`shell.php`), while still passing the whitelist. 
- The same may be used with web applications hosted on a **Windows server by injecting a colon (`:`) before the allowed file extension (e.g. `shell.aspx:.jpg`),** which should also write the file as (`shell.aspx`). 
- Similarly, each of the other characters has a use case that may allow us to upload a PHP script while bypassing the type validation test.

	- We can write a small bash script that **generates all permutations of the file name,** where the above characters would be injected before and after both the `PHP` and `JPG` extensions, as follows:

```bash
		for char in '%20' '%0a' '%00' '%0d0a' '/' '.\\' '.' '…' ':'; do
		    for ext in '.php' '.phps'; do
		        echo "shell$char$ext.jpg" >> wordlist.txt
		        echo "shell$ext$char.jpg" >> wordlist.txt
		        echo "shell.jpg$char$ext" >> wordlist.txt
		        echo "shell.jpg$ext$char" >> wordlist.txt
		    done
		done
```

- With this custom wordlist, **we can run a fuzzing scan with `Burp Intruder`**, similar to the ones we did earlier. 
- If either the **==back-end or the web server is outdated or has certain misconfigurations, some of the generated filenames may bypass the whitelist test and execute PHP code.==**

## Assignment
----
- The following worked, **misconfiguration in php.conf in Apache2**:
```
	┌──(drasstrange㉿kali)-[~/CPTS_Path]
	└─$ curl 'http://154.57.164.81:30898/profile_images/image.phtml.jpg?cmd=cat+/flag.txt'
	HTB{1_wh173l157_my53lf}
```

