# Blacklisting Extensions
---
- When we get `Extension not allowed`. This indicates that the web application **may have some form of file type validation on the back-end, in addition to the front-end validations.**
- There are generally two common forms of validating a file extension on the back-end:
	1. Testing against a **`blacklist` of types**
	2. Testing against a **`whitelist` of types**
- Furthermore, the validation **may also check the `file type` or the `file content`** for type matching.
- The **weakest form** of validation amongst these is **`testing the file extension against a blacklist of extension`**
```php
		$fileName = basename($_FILES["uploadFile"]["name"]);
		$extension = pathinfo($fileName, PATHINFO_EXTENSION);
		$blacklist = array('php', 'php7', 'phps');
		
		if (in_array($extension, $blacklist)) {
		    echo "File type not allowed";
		    die();
		}
```
- **==`It is not comprehensive`, as many other extensions are not included in this list,==** which may still be used to execute PHP code on the back-end server if uploaded.
	- **Tip:** The comparison ==**above is also case-sensitive,**== and is only considering lowercase extensions. **==In Windows Servers, file names are case insensitive,**== so we may try uploading a `php` with a mixed-case (e.g. `pHp`), which may bypass the blacklist as well, and should still execute as a PHP script.