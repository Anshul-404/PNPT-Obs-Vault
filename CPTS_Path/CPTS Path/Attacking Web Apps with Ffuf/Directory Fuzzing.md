# Ffuf
---
- `ffuf -h`
- ![[Pasted image 20251019062615.png]]

# Directory Fuzzing
---
- As we can see from the example above, the main two options are **==`-w` for wordlists and `-u` for the URL.==** 
- We can assign a **wordlist to a keyword to refer to it where we want to fuzz.** For example, ==we can pick **our wordlist and assign the keyword `FUZZ` to it by adding `:FUZZ` after it:**==
	- `ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ`
- Next, as we want to be fuzzing for web directories, **we can place the `FUZZ` keyword where the directory would be within our URL, with:**
	- `ffuf -w <SNIP> -u http://SERVER_IP:PORT/FUZZ`

- `ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ`

- We can even m**ake it go faster if we are in a hurry by increasing the number of threads to 200, for example, with `-t 200`**, but this is not recommended, especially when used on a remote site, **==as it may disrupt it, and cause a `Denial of Service`==**