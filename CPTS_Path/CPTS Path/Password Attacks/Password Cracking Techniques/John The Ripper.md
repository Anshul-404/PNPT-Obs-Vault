	- [John the Ripper](https://github.com/openwall/john) (aka. `JtR` aka. `john`) is a well-known penetration testing tool used fo**r cracking passwords through various attacks including brute-force and dictionary.**
- The **`"jumbo"` variant** is recommended for our uses, as it has performance optimizations, additional features such as multilingual word lists.

# Cracking modes
---
## Single crack mode
- `Single crack mode` is a **rule-based cracking technique that is most useful when targeting Linux credentials.** 
- It **==generates password candidates based on the victim's username, home directory name, and [GECOS](https://en.wikipedia.org/wiki/Gecos_field) values==** (full name, room number, phone number, etc.). 
- These strings are **run against a large set of rules that apply common string modifications** seen in passwords (e.g. a user whose real name is `Bob Smith` might use `Smith1` as their password).

- Imagine we as attackers came across the file `passwd` with the following contents:

```
r0lf:$6$ues25dIanlctrWxg$nZHVz2z4kCy1760Ee28M1xtHdGoy0C2cYzZ8l2sVa1kIa8K9gAcdBP.GI6ng/qA4oaMrgElZ1Cb9OeXO4Fvy3/:0:0:Rolf Sebastian:/home/r0lf:/bin/bash
```

-  Running the attack:
	- `john --single hash.txt`

## Wordlist mode
- `Wordlist mode` is used to crack passwords with a dictionary attack, meaning it **attempts all passwords in a supplied wordlist** against the password hash. The basic syntax for the command is as follows:
	- `john --wordlist=<wordlist_file> <hash_file>`

## Incremental mode
- `Incremental mode` is a powerful, brute-force-style password cracking mode that generates candidate passwords based on a statistical model ([Markov chains](https://en.wikipedia.org/wiki/Markov_chain)).
- This mode is the most exhaustive, **but also the most time-consuming.** It generates **password guesses dynamically and does not rely on a predefined wordlist**, in contrast to wordlist mode.
- Incremental mode **==uses a statistical model to make educated guesses==**, resulting in a significantly more efficient approach than naïve brute-force attacks:
	- `john --incremental <hash_file>`

# Identifying hash formats
---
- `hashid -j 193069ceb0461e1d40d216e32c79c704`
- `hash-identifier`
- ` john --wordlist=/usr/share/wordlists/rockyou.txt RIPEMD-128_hash --format=ripemd-128`

# Cracking files
---
- It is also possible to crack password-protected or encrypted files with JtR. 
- Multiple **`"2john"` tools come with JtR** that can be used to process files and produce hashes compatible with JtR:
	- `<tool> <file_to_crack> > file.hash`
![[Pasted image 20250628080611.png]]

