# Automated Discovery
---
- Almost **all Web Application Vulnerability Scanners (like [Nessus](https://www.tenable.com/products/nessus), [Burp Pro](https://portswigger.net/burp/pro), or [ZAP](https://www.zaproxy.org/)) have various capabilities for detecting all three types of XSS** vulnerabilities.
- These scanners usually do two types of scanning: 
	- A **Passive Scan**, which **reviews client-side code for potential DOM-based vulnerabilities**, and 
	- an **Active Scan,** which **sends various types of payloads to attempt to trigger an XSS through payload injection** in the page source.

- While **paid tools usually have a higher level of accuracy in detecting XSS vulnerabilities** (especially when security bypasses are required), **we can still find open-source tools that can assist us in identifying potential XSS** vulnerabilities. 
- Such tools usually **work by identifying input fields in web pages, sending various types of XSS payloads**, and then **comparing the rendered page source to see if the same payload can be found in it**, which may indicate a successful XSS injection.

- Some of the **common open-source tools that can assist us in XSS discovery are [XSS Strike](https://github.com/s0md3v/XSStrike), [Brute XSS](https://github.com/rajeshmajumdar/BruteXSS), and [XSSer](https://github.com/epsylon/xsser)**. 
- We ca**n try `XSS Strike`** by cloning it to our VM with `git clone`:
	- `git clone https://github.com/s0md3v/XSStrike.git`
	- `cd XSStrike`
	- `pip install -r requirements.txt`
	- `python xsstrike.py`
- We can then **run the script and provide it a URL with a parameter using `-u`**. Let's try using it with our `Reflected XSS` example from the earlier section:
	- `python xsstrike.py -u "http://SERVER_IP:PORT/index.php?task=test" `
	- ![[Pasted image 20251028073258.png]]

# Manual Discovery
---
- When it comes to manual XSS discovery, the difficulty of finding the XSS vulnerability **depends on the level of security of the web application.**
- **Basic XSS vulnerabilities can usually be found through testing various XSS payloads,** but identifying **advanced XSS vulnerabilities requires advanced code review skills.**

## XSS Payloads
- The most basic method of looking for XSS vulnerabilities is manually testing **various XSS payloads against an input field** in a given web page.
- We can **==find huge lists of XSS payloads online, like the one on [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/README.md) or the one in [PayloadBox](https://github.com/payloadbox/xss-payload-list).==** 
- We can then **begin testing these payloads one by one by copying each one** and adding it in our form, and seeing whether an alert box pops up.

- Note: **XSS can be injected into any input in the HTML page, which is not exclusive to HTML input fields**, but may also be in **==HTTP headers like the Cookie or User-Agent==** (i.e., when their values are displayed on the page).

- You will notice that the **majority of the above payloads do not work with our example web applications**, even though we are dealing with the most basic type of XSS vulnerabilities.
- This is because these payloads are **written for a wide variety of injection points (like injecting after a single quote)** or are **designed to evade certain security measures (like sanitization filters).**
- Furthermore, such payloads utilize a variety of injection vectors to execute JavaScript code, like basic `<script>` tags, other `HTML Attributes` like `<img>`, or even `CSS Style` attributes.

- This is why it is **not very efficient to resort to manually copying/pasting XSS** payloads, as even if a web application is vulnerable, **it may take us a while to identify the vulnerability, especially if we have many input fields to test.**
- This is **==why it may be more efficient to write our own Python script to automate sending these payloads and then comparing the page source==** to see how our payloads were rendered.

# Code Review
---
- The most reliable method of **detecting XSS vulnerabilities is manual code review,** which should cover both back-end and front-end code.
- If we understand precisely **how our input is being handled all the way until it reaches the web browser**, **we can write a custom payload that should work** with high confidence.
- In the previous section, we looked at a basic example of HTML code review when discussing the `Source` and `Sink` for DOM-based XSS vulnerabilities. **This gave us a quick look at how front-end code review works in identifying XSS** vulnerabilities, although on a very basic front-end example.
- These are also advanced techniques that are out of the scope of this module. Still, if you are interested in learning them, the [Secure Coding 101: JavaScript](https://academy.hackthebox.com/course/preview/secure-coding-101-javascript) and the [Whitebox Pentesting 101: Command Injection](https://academy.hackthebox.com/course/preview/whitebox-pentesting-101-command-injection) modules thoroughly cover this topic.