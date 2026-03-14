	- Many modern web servers and web applications also **test the content of the uploaded file** to ensure it matches the specified type.
- There are two common methods for validating the file content: **==`Content-Type Header` or `File Content`.==** Let's see how we can identify each filter and how to bypass both of them.

## Content-Type
---
- The following is an example of how a PHP web application tests the Content-Type header to validate the file type:
```php
	$type = $_FILES['uploadFile']['type'];
	
	if (!in_array($type, array('image/jpg', 'image/jpeg', 'image/png', 'image/gif'))) {
	    echo "Only images are allowed";
	    die();
	}
```
- The code sets the (`$type`) variable from the uploaded file's `Content-Type` header. 
- **==Our browsers automatically set the Content-Type header when selecting a file==** through the file selector dialog, usually derived from the file extension.
- We may start by **==fuzzing the Content-Type header with SecLists' [Content-Type Wordlist](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-all-content-types.txt)==** through Burp Intruder, to see which types are allowed.
- However, the message tells us that only images are allowed, **so we can limit our scan to image types**, which reduces the wordlist to `45` types
	- ![[Pasted image 20260301235954.png]]
**Note:** A file upload HTTP request has two Content-Type headers, one for the attached file (at the bottom), and one for the full request (at the top). We usually need to **modify the file's Content-Type header**, but in some cases the request will only contain the main Content-Type header (e.g. if the uploaded content was sent as `POST` data), in which case we will need to modify the main Content-Type header.
## MIME-Type
---
- The second and more common type of file content validation is testing the uploaded file's `MIME-Type`. 
- `Multipurpose Internet Mail Extensions (MIME)` is an internet standard that **==determines the type of a file through its general format and bytes structure.==**
- This is usually done by inspecting the first few bytes of the file's content, **==which contain the [File Signature](https://en.wikipedia.org/wiki/List_of_file_signatures) or [Magic Bytes](https://web.archive.org/web/20240522030920/https://opensource.apple.com/source/file/file-23/file/magic/magic.mime).==**
- If we **change the first bytes of any file to the GIF magic bytes, its MIME type would be changed to a GIF image**, regardless of its remaining content or extension.
**Tip:** Many other image types **have non-printable bytes for their file signatures**, while a `**GIF` image starts with ASCII printable bytes** (as shown above), so it is the **easiest to imitate.** 
**==Furthermore, as the string `GIF8` is common between both GIF signatures, it is usually enough to imitate a GIF image.==**
	![[Pasted image 20260302001548.png]]
- The following example shows how a PHP web application can test the MIME type of an uploaded file:
```php
	$type = mime_content_type($_FILES['uploadFile']['tmp_name']);
	
	if (!in_array($type, array('image/jpg', 'image/jpeg', 'image/png', 'image/gif'))) {
	    echo "Only images are allowed";
	    die();
	}
```
- As we can see, the MIME types are similar to the ones found in the Content-Type headers, but their source is different, as **==PHP uses the `mime_content_type()` function to get a file's MIME type.==**
	- ![[Pasted image 20260302001704.png]]

For example, we can try using an `Allowed MIME type with a disallowed Content-Type`, an `Allowed MIME/Content-Type with a disallowed extension`, or a `Disallowed MIME/Content-Type with an allowed extension`, and so on. Similarly, **we can attempt other combinations and permutations to try to confuse the web server, a**nd depending on the level of code security, we may be able to bypass various filters.

## Assessment
---
- Upload the file using `gif` magic bytes and double extension (.jpg.phtml) to bypass extension filter:
		- ![[Pasted image 20260302003522.png]]
			- ![[Pasted image 20260302003543.png]]