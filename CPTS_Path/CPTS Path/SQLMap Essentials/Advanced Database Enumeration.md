# DB Schema Enumeration
---
- If we wanted to **retrieve the structure of all of the tables** so that we can have a complete overview of the database architecture, we could use the **switch `--schema`:**
	- `sqlmap -u "http://www.example.com/?id=1" --schema`
	- ![[Pasted image 20251027060511.png]]

# Searching for Data
---
- When dealing with **complex database structures** with numerous tables and columns, **we can search for databases, tables, and columns of interest,** **by using the `--search` option.**
	- `sqlmap -u "http://www.example.com/?id=1" --search -T user`
		- Searches for tables like user
	- ![[Pasted image 20251027060606.png]]
- We could also have tried to **==search for all column names based on a specific keyword (e.g. `pass`):==**
	- `sqlmap -u "http://www.example.com/?id=1" --search -C pass`
	- ![[Pasted image 20251027060648.png]]

# Password Enumeration and Cracking
---
- Once we identify a **table containing passwords** (e.g. `master.users`), we can **retrieve that table with the `-T` option, as previously shown**:
	- `sqlmap -u "http://www.example.com/?id=1" --dump -D master -T users`
- We can see in the previous example that SQLMap has automatic password hashes cracking capabilities. 
- Upon retrieving **any value that resembles a known hash format, SQLMap prompts us to perform a dictionary-based attack on the found hashes.**

# DB Users Password Enumeration and Cracking
---
- Apart from user credentials found in DB tables, we can also **==attempt to dump the content of system tables containing database-specific credentials (e.g., connection credentials).==**
- To ease the whole process, SQLMap has a **==special switch `--passwords`==** designed especially for such a task:
	- `sqlmap -u "http://www.example.com/?id=1" --passwords --batch`
	- ![[Pasted image 20251027060838.png]]
	- ![[Pasted image 20251027060845.png]]
- Tip: **The '--all' switch in combination with the '--batch' switch, will automa(g)ically do the whole enumeration process on the target itself, and provide the entire enumeration details.**