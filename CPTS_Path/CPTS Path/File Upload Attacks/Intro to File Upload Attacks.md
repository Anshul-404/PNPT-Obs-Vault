- File upload vulnerabilities **are amongst the most common vulnerabilities** found in web and mobile applications, as we can see in the latest [CVE Reports](https://www.cvedetails.com/vulnerability-list/cweid-434/vulnerabilities.html)
- We will also notice that most of these vulnerabilities are **scored as `High` or `Critical` vulnerabilities, showing the level of risk** caused by insecure file upload.

# Types of File Upload Attacks
---
- The most common reason behind file upload vulnerabilities is w**eak file validation and verification**, which may not be well secured to prevent unwanted file types or could be missing altogether.
- The **worst possible** kind of file upload vulnerability is an **`unauthenticated arbitrary file upload` vulnerability.**

- The **most common and critical attack caused by arbitrary file uploads** is **==`gaining remote command execution` over the back-end server by uploading a web shell==** or uploading a script that sends a reverse shell.

- Examples of these attacks include:
	- **Introducing other vulnerabilities like `XSS` or `XXE`.**
	- Causing a **`Denial of Service (DoS)`** on the back-end server.
	- **Overwriting critical system files** and configurations.
	- And many others.

- Finally, a file upload vulnerability **is not only caused by writing insecure functions but is also often caused by the use of outdated libraries** that may be vulnerable to these attacks