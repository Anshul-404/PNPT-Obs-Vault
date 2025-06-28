- We may find publicly available file upload forms that let us directly upload a PHP, JSP, or ASP.NET web shell. 
- We may also come across applications such as Tomcat, Axis2, or WebLogic, which allow you to deploy JSP code via a WAR file as part of their functionality.

# What is a Web Shell?
---
- A `web shell` **is a browser-based shell session we can use to interact with the underlying operating system of a web server.** 
- Again, to gain remote code execution via web shell, we must first find a website or web application vulnerability that can give us file upload capabilities.

# Laudanum, One Webshell to Rule Them All
---
- Laudanum is a **repository of ready-made files that can be used to inject onto a victim** and receive back access via a reverse shell, run commands on the victim host right from the browser, and more.
- he repo includes injectable files for many **different web application languages to include `asp, aspx, jsp, php,` and more.**
- The Laudanum files can be found in the `/usr/share/laudanum` directory.
- For specific files such as the shells, **you must edit the file first to insert your `attacking` host IP address to ensure you can access the web shell** or receive a callback in the instance that you use a reverse shell.
- **Add your IP address to the `allowedIps` variable** on line `59`. **Make any other changes you wish. It can be prudent to remove the ASCII art and comments from** the file. These items in a payload are often signatured on and c**an alert the defenders/AV to what you are doing.**
- ![[Pasted image 20250625055525.png]]