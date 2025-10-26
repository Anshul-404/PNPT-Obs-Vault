# Display Errors
---
- The first step is **usually to switch the `--parse-errors`, to parse the DBMS errors** (if any) and displays them as part of the program run

# Store the Traffic
---
- The **`-t` option stores the whole traffic content to an output file:**
	- `sqlmap -u "http://www.target.com/vuln.php?id=1" --batch -t /tmp/traffic.txt`
- As we can see from the above output, **the `/tmp/traffic.txt` file now contains all sent and received HTTP requests.**

# Verbose Output
---
- Another useful flag is the `-v` option, which raises the verbosity level of the console output:
	- `sqlmap -u "http://www.target.com/vuln.php?id=1" -v 6 --batch`
- As we can see, **the `-v 6` option will directly print all errors and full HTTP request to the terminal** so that we can follow along with **everything SQLMap is doing in real-time.**

# Using Proxy
---
- Finally, we can **utilize the `--proxy` option to redirect the whole traffic through a (MiTM) proxy** (e.g., `Burp`).
- This will **route all SQLMap traffic through `Burp`, so that we can later manually investigate all requests, repeat them, and utilize all features of `Burp` with these requests.**