# Understanding Login Forms
---
- While login forms may appear as simple boxes soliciting your username and password, they **represent a complex interplay of client-side and server-side technologies.**
- A **form, when submitted, sends a POST request to the `/login` endpoint** on the server, **including** the **entered username and password as form data.**
	- ![[Pasted image 20251021072349.png]]

# http-post-form
---
- Hydra's **`http-post-form` service is specifically designed to target login forms**. 
- It enables the **automation of POST requests**, **==dynamically inserting username and password combinations==** into the request body.
- The general structure of a Hydra command using `http-post-form` looks like this:
	- `hydra [options] target http-post-form "path:params:condition_string"`

## Understanding the Condition String
- In Hydra’s `http-post-form` module, success and failure conditions are crucial for properly identifying valid and invalid login attempts.
- Hydra **==primarily relies on failure conditions (`F=...`) to determine==** when a login attempt has failed, **==but you can also specify a success condition (`S=...`) to indicate when a login is successful.==**:
	- `hydra ... http-post-form "/login:user=^USER^&pass=^PASS^:F=Invalid credentials"`
- In this case, Hydra will **check each response for the string "Invalid credentials."** If it finds this phrase, **it will mark the login attempt as a failure** and move on to the next username/password pair.
- For instance, if the ==**application redirects the user after a successful login**== (using HTTP status code `302`), or displays specific content (like "Dashboard" or "Welcome"), you can ==configure== Hydra to look for that **==success condition using `S=`==**:
	- `hydra ... http-post-form "/login:user=^USER^&pass=^PASS^:S=302"`
	- In this case, Hydra will treat **any response that returns an HTTP 302 status code as a successful login.**
- Similarly, if a **==successful login results in content like "Dashboard" appearing on the page,==** you can configure Hydra to look for that keyword as a success condition:
	- `hydra ... http-post-form "/login:user=^USER^&pass=^PASS^:S=Dashboard"`

# Constructing the params String for Hydra
---
- After analyzing the login form's structure and behavior, it's time to build the `params` string, a critical component of Hydra's `http-post-form` attack module.
- The `params` string **consists of key-value pairs, similar to how data is encoded in a POST request.** Each pair represents a field in the login form, with its corresponding value:
	- `Form Parameters`: These are the essential fields that hold the us**ername and password.** Hydra will **dynamically replace placeholders (`^USER^` and `^PASS^`)** within these parameters with values from your wordlists.
	- `Additional Fields`: If the **form includes other hidden fields or tokens (e.g., CSRF tokens),** they must also be included in the `params` string.
	- `uccess Condition`: This defines the **criteria Hydra will use to identify a successful login.** It can be an HTTP status code **(like `S=302` for a redirect)** or the presence or **absence of specific text in the server's response (e.g., `F=Invalid credentials` or `S=Welcome`).**


# Wordlists
- We will be using [top-usernames-shortlist.txt](https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Usernames/top-usernames-shortlist.txt) for the username list, and [2023-200_most_used_passwords.txt](https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/2023-200_most_used_passwords.txt) for the password list.
- This `params` string is incorporated into the Hydra command as follows. **Hydra will systematically substitute `^USER^` and `^PASS^` with values from your wordlists**, sending POST requests to the target and analyzing the responses for the specified failure condition.
	- `curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/master/Usernames/top-usernames-shortlist.txt`
	- `curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/2023-200_most_used_passwords.txt`
	
	- ` hydra -L top-usernames-shortlist.txt -P 2023-200_most_used_passwords.txt -f IP -s 5000 http-post-form "/:username=^USER^&password=^PASS^:F=Invalid credentials"`