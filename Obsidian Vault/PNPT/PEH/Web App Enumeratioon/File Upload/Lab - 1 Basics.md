![[Pasted image 20250407103534.png]]

# Information
---
- Looking at the `network` tab, we can see that hitting `Browser` and selecting a file **does not make any `server` side requests.**
- ==This means, the check is done client side only using `js`.== This is a good indicator that we can bypass this.
	- ![[Pasted image 20250407103757.png]]
- We can see the `js` code using `view page source`:
	```js
	 <script>
	    function validateFileInput(input) {
	        var validExtensions = ['jpg', 'png'];
	        var fileName = input.files[0].name;
	        var fileNameExt = fileName.substr(fileName.lastIndexOf('.') + 1);
	        if (!validExtensions.includes(fileNameExt.toLowerCase())) {
	            input.value = '';
	            alert("Only '.jpg' and '.png' files are allowed.");
	        }
	    }
	    </script>
	```
	- The code checks that extension must be `jpg` or `png`.
- ==After `selecting` the file, we can change its extension using `burpsuite` before it reaches the server.==

# Exploitation
---
- Rename reverse `shell.php` to `shell.png`.
	- ![[Pasted image 20250407104603.png]]
- Select `Browser` and select `shell.png` and then `upload`
- Intercept this request in `burpsuite` and then change the file name to `shell.php`
	- ![[Pasted image 20250407105103.png]]
	- ![[Pasted image 20250407105119.png]]
	- ![[Pasted image 20250407105148.png]]
- Find the file and access it through browser
	- ![[Pasted image 20250407105304.png]]
- Most likely in http://localhost/labs/uploads folder => http://localhost/labs/uploads/shell.php
	- ![[Pasted image 20250407105409.png]]
