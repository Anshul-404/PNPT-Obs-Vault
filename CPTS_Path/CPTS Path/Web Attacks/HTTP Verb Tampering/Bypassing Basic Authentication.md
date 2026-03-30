- Exploiting HTTP Verb Tampering vulnerabilities is usually a relatively straightforward process. 
- We just need to **try alternate HTTP methods to see how they are handled by the web server** and the web application. 
- The first type of HTTP Verb Tampering vulnerability is mainly caused by `Insecure Web Server Configurations`, and exploiting this vulnerability can allow us to bypass the HTTP Basic Authentication prompt on certain pages.

# Identify
---
- We see that we have a basic `File Manager` web application, in which we can add new files by typing their names and hitting `enter`:
	- ![[Pasted image 20260329015553.png]]
- However, suppose we try to **delete all files by clicking on the red `Reset` button**. 
- In that case, we see that this functionality seems to be **==restricted for authenticated users only,==** as we get the following `HTTP Basic Auth` prompt:
	- ![[Pasted image 20260329015630.png]]
- As we do not have any credentials, we will get a `401 Unauthorized` page:
	- ![[Pasted image 20260329015643.png]]

- So, let's see whether we can bypass this with an HTTP Verb Tampering attack. To do so, we need to **==identify which pages are restricted by this authentication.==** 
- If we examine the HTTP request after clicking the Reset button or **look at the URL that the button navigates** to after clicking it, we see that it is at `/admin/reset.php`. 
- So, either the `/admin` directory is restricted to authenticated users only, or only the `/admin/reset.php` page is. 
- We can confirm this **by visiting the `/admin` directory, and we do indeed get prompted to log in again**. This means that **==the full `/admin` directory is restricted.==**

## Exploit
---
- To try and exploit the page, we need to identify the HTTP request method used by the web application. We can intercept the request in Burp Suite and examine it:
	- ![[Pasted image 20260329015908.png]]
- As the page uses a `GET` request, we can **send a `POST` request and see whether the web page allows** `POST` requests (i.e., whether the Authentication covers `POST` requests). 
- To do so, **we can right-click on the intercepted request in Burp and select `Change Request Method`,** and it will automatically change the request into a `POST` request:
	- ![[Pasted image 20260329015940.png]]
	- ![[Pasted image 20260329015955.png]]
- So, it seems like the web server configurations do cover both `GET` and `POST` requests. However, as we have previously learned, we can utilize many other HTTP methods, most notably the `HEAD` method, which is identical to a `GET` request but does not return the body in the HTTP response. If this is successful, we may not receive any output, but the `reset` function should still get executed, which is our main target.

- To see whether the **server accepts `HEAD` requests, we can send an `OPTIONS` request to it and see what HTTP methods are accepted**, as follows:
	- ![[Pasted image 20260329021211.png]]
- As we can see, the response shows `Allow: POST,OPTIONS,HEAD,GET`, which means that the web server i**ndeed accepts `HEAD` requests, which is the default configuration for many web servers.**
- So, let's **try to intercept the `reset` request again, and this time use a `HEAD` request** to see how the web server handles it:
	![HTTP HEAD request to /admin/reset.php with headers and user-agent details.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/134/web_attacks_verb_tampering_HEAD_request.jpg)

- Once we change `POST` to `HEAD` and forward the request, we will see that **==we no longer get a login prompt or a `401 Unauthorized` page and get an empty output instead==**, as expected with a `HEAD` request.
- If we go back to the `File Manager` web application, we will see that **all files have indeed been deleted,** meaning that we successfully triggered the `Reset` functionality without having admin access or any credentials.