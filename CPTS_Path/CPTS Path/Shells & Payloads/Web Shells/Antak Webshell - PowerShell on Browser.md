# ASPX Explained
---
- **Active Server Page Extended (ASPX) is a file type/extension written for Microsoft's ASP.NET Framework.**
- On a web server running the ASP.NET framework, **web form pages can be generated for users to input data.** On the server side, the information will be converted into HTML. 
- We can take advantage of this by using a**n ASPX-based web shell to control the underlying Windows operating system**.

# Antak Webshell
---
- Antak is a **web shell built in ASP.Net included within the [Nishang project](https://github.com/samratashok/nishang)**. Nishang is an Offensive PowerShell toolset that can provide options for any portion of your pentest.
- Antak **utilizes PowerShell to interact with the host**, making it great for acquiring a web shell on a Windows server.
- The Antak files can be found in the **`/usr/share/nishang/Antak-WebShell`** directory.

- Make **sure you set credentials for access to the web shell.** Modify `line 14`, adding a user and password .
- ![[Pasted image 20250625061415.png]]