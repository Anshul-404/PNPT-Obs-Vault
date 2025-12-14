- If we had **dozens of directories, each with their own subdirectories and files,** this would take a very long time to complete. To be able to **==automate this, we will utilize what is known as `recursive fuzzing`.==**

# Recursive Flags
---
- When we scan recursively, **it automatically starts another scan under any newly identified directories** that may have on their pages **until it has fuzzed the main website and all of its subdirectories.**
- Always **advised to specify a `depth` to our recursive scan**, such that it will not scan directories that are deeper than that depth.
- In `ffuf`, we can enable recursive scanning **==with the `-recursion` flag, and we can specify the depth with the `-recursion-depth` flag==**
- Finally, we will also **add the flag `-v` to output the full URLs.** Otherwise, it may be difficult to tell which `.php` file lies under which directory.

# Recursive Scanning
---
- `ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v`