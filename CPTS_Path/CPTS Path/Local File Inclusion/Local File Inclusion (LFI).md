# Basic LFI
---- 
- The exercise we have at the end of this section shows us an example of a web app that allows users to set their language to either English or Spanish:
	- ![[Pasted image 20251101053446.png]]
- We also notice that the URL includes a **==`language` parameter that is now set to the language we selected (`es.php`).==**
- Two **common readable files that are available on most back-end servers** are ==**`/etc/passwd`**== on Linux and **==`C:\Windows\boot.ini`==** on Windows. So, let's change the parameter from `es` to `/etc/passwd`:
	- ![[Pasted image 20251101053556.png]]

# Path Traversal
---
- In the earlier example, **we read a file by specifying its `absolute path` (e.g. `/etc/passwd`)**. 
- This **==would work if the whole input was used within the `include()` function==** without any additions, like the following example:
	- `include($_GET['language']);`

- However, in many occasions, **==web developers may append or prepend a string to the `language` parameter==**. 
- For example, t**he `language` parameter may be used for the filename**, and may be added after a directory, as follows:
	- `include("./languages/" . $_GET['language']);`

- In this case, **if we attempt to read `/etc/passwd`**, then the path passed **==to `include()` would be (`./languages//etc/passwd`),==**
- We can easily bypass this restriction by traversing directories u**sing `relative paths`.**
- To do so, we can **add `../` before our file name**.
- So, we can use this trick **==to go back several directories until we reach the root path (i.e. `/`),==** and then specify our absolute file path (e.g. `../../../../etc/passwd`), and the file should exist:
	- ![[Pasted image 20251101053814.png]]

- This trick would work **even if the entire parameter was used in the `include()`** function, so **==we can default to this technique, and it should work in both cases.==**

**Tip:** It can always be **useful to be efficient and not add unnecessary `../` several** times, especially if **we were writing a report or writing an exploit.**
- For example, **with `/var/www/html/` we are `3` directories away** from the root path, **so we can use `../` 3 times (i.e. `../../../`).**

# Filename Prefix
---
- On some occasions, **==our input may be appended after a different string.==**
- For example, **it may be used with a prefix to get the full filename**, like the following example:
	- `include("lang_" . $_GET['language']);`
- In this case, if we try to traverse the directory with `../../../etc/passwd`, the **==final string would be `lang_../../../etc/passwd`==**, **which is invalid.**

- So, instead of directly using path traversal, **==we can prefix a `/` before our payload, and this should consider the prefix as a directory==**, and then **we should bypass the filename** and be able to traverse directories:
	- `lang_/../../../etc/passwd`
	- ![[Pasted image 20251101054644.png]]

- **Note:** This may not always work, **as in this example a directory named `lang_/` may not exist**, so our relative path may not be correct.

# Appended Extensions
---
- Another very common example is **when an extension is appended to the `language` parameter**, as follows:
	- `include($_GET['language'] . ".php");`

- This is ==**quite common**==, as in this case, **we would not have to write the extension every time we need to change the language**. This **==may also be safer as it may restrict us==** to only including PHP files.
- In this case, if we try to read `/etc/passwd`, then the **==file included would be `/etc/passwd.php`==**, which does not exist.
- There are several **techniques that we can use to bypass this**, and we will **discuss them in upcoming sections.**

# Second-Order Attacks
---
- Another common, and a **little bit more advanced**, LFI attack is a **`Second Order Attack`.**
- For example, a web application **may allow us to download our avatar through a URL like (`/profile/$username/avatar.png`)**. 
- If we **==craft a malicious LFI username (e.g. `../../../etc/passwd`),==** then it may be possible to **change the file being pulled to another local file** on the server and grab it **instead of our avatar.**
- In this case, we would be **poisoning a database entry with a malicious LFI** payload in our username.
- Then, **another web application functionality would utilize this poisoned entry to perform our attack** (i.e. download our avatar based on username value).
- This is why this **attack is called a `Second-Order` attack.**