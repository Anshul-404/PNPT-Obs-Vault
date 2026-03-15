This section will look at a few examples of how command injections may be detected and blocked and how we can identify what is being blocked.

# Filter/WAF Detection
---
- Let us start by visiting the web application in the exercise at the end of this section. We see the same `Host Checker` web application we have been exploiting, but now it has a few mitigations up its sleeve. 
- We can see that if we try the previous operators we tested, like (`;`, `&&`, `||`), **we get the error message `invalid input`**:
	- ![[Pasted image 20260316005409.png]]
- This indicates that something we sent ==**triggered a security mechanism**== in place that denied our request.
- If the **==error message displayed a different page, with information like our IP and our request, this may indicate that it was denied by a WAF.==**
- Other than the IP (which we know is not blacklisted), we sent:

	1. A semi-colon character `;`
	2. A space character
	3. A `whoami` command

- So, the web application **==either `detected a blacklisted character` or `detected a blacklisted command`, or both==**. So, let us see how to bypass each.

## Blacklisted Characters
---
- A web application may have a list of blacklisted characters, and if the command contains them, it would deny the request. 
- The `PHP` **code may look something like the following**:
```php
	$blacklist = ['&', '|', ';', ...SNIP...];
	foreach ($blacklist as $character) {
	    if (strpos($_POST['ip'], $character) !== false) {
	        echo "Invalid input";
	    }
	}
```
- If any character in the string we sent matches a character in the blacklist, our request is denied.

## Identifying Blacklisted Character
---
- Let us reduce our **==request to one character at a time and see when it gets blocked==**. 
- We know that the (`127.0.0.1`) payload does work, so let us start by adding the semi-colon (`127.0.0.1;`):
	- ![[Pasted image 20260316010230.png]]