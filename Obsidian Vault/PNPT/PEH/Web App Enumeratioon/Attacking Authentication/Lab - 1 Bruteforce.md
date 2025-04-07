# Tools
---
- `burpsuite, Hydra, ZAP, ffuf`
- Use reasonably sized wordlist to **not get blocked** like `seclists`: `xato-net-10-million-passwords-1000.txt + 10000`

# Burpsuite
---
- Intercept request
- Send to **`intruder` (`ctrl + i`)**
- Selec**t parameter to brute force (`password`) and then click `Add`**
- Select a `wordlist` to bruteforce
- Then **`filter` by length to find odd one out.**
# FFUF
---
- Copy `request` into a file: `request.txt`
	- ![[Pasted image 20250407145942.png]]
- **Rename parameter value to brute force to `FUZZ`. For example:- `password=FUZZ`**
	- ![[Pasted image 20250407150011.png]]
- Filter by `size` using `-fs` param
	- `ffuf -request request_lab1.txt -request-proto http -w /usr/share/seclists/Passwords/xato-net-10-million-passwords-1000.txt -fs 1814`
	- ![[Pasted image 20250407150150.png]]
	- ![[Pasted image 20250407150215.png]]