**Tip:** Fuzzing parameters may expose unpublished parameters that are publicly accessible. **Such parameters tend to be less tested and less secured**, so it is important to test such parameters for the web vulnerabilities we discuss in other modules.

# GET Request Fuzzing
---
- Similarly to how we have been fuzzing various parts of a website, we will use `ffuf` to enumerate parameters. 
- Let us first **start with fuzzing for `GET` requests, which are usually passed right after the URL, with a `?` symbol, like:**
	-  `http://admin.academy.htb:PORT/admin/admin.php?param1=key`.
- So, all we have to do is **replace `param1` in the example above with `FUZZ`** and rerun our scan. 
- Before we can start, however, we must pick an appropriate wordlist. 
- Once again, `SecLists` has just that in **`/opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt`.** 
  
	- `ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key -fs xxx`