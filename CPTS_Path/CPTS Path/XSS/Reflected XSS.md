- There are two types of `Non-Persistent XSS` vulnerabilities:
	- **`Reflected XSS`, which gets processed by the back-end server,** and 
	- **`DOM-based XSS`, which is completely processed on the client-side and never reaches the back-end server.**
- `Non-Persistent XSS` vulnerabilities are temporary and are not persistent through page refreshes. **Hence, our attacks only affect the targeted user and will not affect other users who visit the page.**
- **`Reflected XSS` vulnerabilities occur when our input reaches the back-end server and gets returned to us without being filtered or sanitized.**

# Example: To-Do List app
---
- We can start the server below to practice on a web page vulnerable to a Reflected XSS vulnerability.
- We **==can try adding any `test` string to see how it's handled:==**
	- ![[Pasted image 20251028064240.png]]
- As we can see, **==we get `Task 'test' could not be added.`, which includes our input `test` as part of the error message==**.
- If **==our input was not filtered or sanitized, the page might be vulnerable to XSS==**.
- Often possible in **==error messages and searches where our input is displayed back to us.==**
- We can try the same XSS payload we used in the previous section and click `Add`:
	- ![[Pasted image 20251028064345.png]]
- **If we visit the `Reflected` page again, the error message no longer appears,** and our XSS payload is not executed, **which means that this XSS vulnerability is indeed `Non-Persistent`.**

	- ==`But if the XSS vulnerability is Non-Persistent, how would we target victims with it?`==

- This depends on **which HTTP request is used to send our input to the server**.
- We can ch**eck this through the Firefox `Developer Tools`** by clicking [`CTRL+Shift+I`] and **selecting the `Network` tab.**
- Then, we can **put our `test` payload again and click `Add` to send it:**
	- ![[Pasted image 20251028070425.png]]
- As we can see, the first row shows that **==our request was a `GET` request.==**
- **`GET` request sends their parameters and data as part of the URL**. 
- So, to target a user, **we can send them a URL containing our payload**.