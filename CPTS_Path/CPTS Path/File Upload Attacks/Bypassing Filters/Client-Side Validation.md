Many web applications **only rely on front-end JavaScript code to validate the selected file format before it is uploaded** and would not upload it if the file is not in the required format (e.g., not an image).
# Client-Side Validation
---
- The exercise at the end of this section **shows a basic `Profile Image` functionality**, frequently seen in web applications that utilize user profile features, like social media web applications:
	- ![[Pasted image 20251107005733.png]]

- However, this time, when we get the file selection dialog, **we ==cannot see our `PHP` scripts (or it may be greyed out),== as the dialog appears to be limited to image formats only:**
	- ![[Pasted image 20251107005755.png]]
- We may still s**elect the `All Files` option to select our `PHP` script anyway**, but when **==we do so, we get an error message saying (`Only images are allowed!`), and the `Upload` button gets disabled:==**
	- ![[Pasted image 20251107005815.png]]
- Luckily, **all validation appears to be happening on the front-end,** as the page **never refreshes or sends any HTTP requests** after selecting our file. 
- So, we **should be able to have complete control over these client-side** validations.
- As mentioned earlier, to bypass these protections, **we can either `modify the upload request to the back-end server`**, or we can **`manipulate the front-end code to disable these type validations`.**

## Back-end Request Modification
- Let's start by **examining a normal request through `Burp`.**
- When we select an image, we see that it gets reflected as our profile image, and **when we click on `Upload`, our profile image gets updated and persists through refreshes.**
- This **==indicates that our image was uploaded to the server, which is now displaying it back to us:==**
	- ![[Pasted image 20251107005955.png]]
- If we **capture the upload request with `Burp`**, we see the following request being sent by the web application:
	- ![[Pasted image 20251107010011.png]]
- If the **back-end server does not validate the uploaded file type, then we should theoretically be able to send any file type/content**, and it would be uploaded to the server.

- The two important parts in the **request are `filename="HTB.png"`** and the **file content at the end of the request.**
- If we ==**modify the `filename` to `shell.php` and modify the content to the web shell we used in the previous section**==; we would be uploading a `PHP` web shell instead of an image.

**Note:** We **may also modify the `Content-Type` of the uploaded file,** though this **should not play an important role at this stage**, so we'll keep it unmodified.

## Disabling Front-end Validation
- Another method to bypass client-side validations is through manipulating the front-end code.
- As these functions are being **completely processed within our web browser, we have complete control over them.**
- To start, **we can click [`CTRL+SHIFT+C`] to toggle the browser's `Page Inspector`,** and then **==click on the profile image, which is where we trigger the file selector for the upload form:==**
	- ![[Pasted image 20251107010224.png]]
- This will highlight the following HTML file input on line `18`:
	- `<input type="file" name="uploadFile" id="uploadFile" onchange="checkFile(this)" accept=".jpg,.jpeg,.png">`
- We can **easily modify this and select `All Files`** as we did before, **so it is unnecessary to change this part of the page.**
- The more interesting part is **==`onchange="checkFile(this)"`, which appears to run a JavaScript code==** whenever we select a file, which appears to be doing the file type validation.
- To get the details of this function, we can **go to the browser's `Console` by clicking [`CTRL+SHIFT+K`],** and then we can **type the function's name (`checkFile`) to get its details:**
	- ![[Pasted image 20251107010342.png]]

- We can **==remove this function from the HTML code==** since its primary use appears to be file type validation, and removing it should not break anything.
- To do so, we can **==go back to our inspector, click on the profile image again, double-click on the function name (`checkFile`) on line `18`, and delete it:==**
	- ![[Pasted image 20251107010427.png]]
- With the `checkFile` **function removed from the file input, we should be able to select our `PHP` web shell** through the file selection dialog and upload it normally with no validations, similar to what we did in the previous section.

**Note:-** ==**Change the code AFTER selecting the file** and **BEFORE pressing the Upload button**== as selecting the file can change the code.