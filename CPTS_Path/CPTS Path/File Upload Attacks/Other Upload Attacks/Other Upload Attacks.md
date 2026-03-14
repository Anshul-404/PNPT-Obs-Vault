## Injections in File Name
---
- We can try **injecting a command in the file name**, and if the web application **==uses the file name within an OS command, it may lead to a command injection attack.==**
- For example, if we name a file **==`file$(whoami).jpg` or ``file`whoami`.jpg`` or `file.jpg||whoami`==**, and then the web application attempts to move the uploaded file with an OS command (e.g. `mv file /tmp`), then our file name would inject the `whoami` command, which would get executed, leading to remote code execution.
- Similarly, **we may use an XSS payload in the file name** (e.g. `<script>alert(window.origin);</script>`), **==which would get executed on the target's machine if the file name is displayed to them==**.

## Upload Directory Disclosure
---
- ![[Pasted image 20260302012700.png]]
## Windows-specific Attacks
---
- One such attack is **using reserved characters, such as (`|`, `<`, `>`, `*`, or `?`),** which are usually reserved for special uses like wildcards.
- If the web application does not properly sanitize these names or wrap them within quotes, ==**they may refer to another file (which may not exist)**== and cause an error that discloses the upload directory.
- Similarly, we may use **==Windows reserved names for the uploaded file name, like (`CON`, `COM1`, `LPT1`, or `NUL`), which may also cause an error==** as the web application will not be allowed to write a file with this name.
- Finally, we may utilize the Windows [8.3 Filename Convention](https://en.wikipedia.org/wiki/8.3_filename) to **overwrite existing files or refer to files that do not exist**. Older versions of Windows were limited to a short length for file names, so **they used a Tilde character (`~`) to complete the file name, which we can use to our advantage.**

- For example, to refer to a file called (`hackthebox.txt`) we can use (`HAC~1.TXT`) or (`HAC~2.TXT`), **where the digit represents the order of the matching files that start with (`HAC`).** As Windows still supports this convention, we can write a file called (e.g. `WEB~1.CON`) to overwrite the `web.conf` file.

## Advanced File Upload Attacks
---
- In addition to all of the attacks we have discussed in this module, there are more advanced attacks that can be used with file upload functionalities. 
- Any **==automatic processing that occurs to an uploaded file, like encoding a video, compressing a file, or renaming a file, may be exploited==** if not securely coded.
- Some commonly used libraries may have public exploits for such vulnerabilities, like the **==AVI upload vulnerability leading to XXE in `ffmpeg`.==**