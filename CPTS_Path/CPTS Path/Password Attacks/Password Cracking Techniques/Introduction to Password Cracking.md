- Passwords are **commonly `hashed` when stored**, **in order to provide some protection in the event they fall into the hands of an attacker.** 
- **`Hashing` is a mathematical function which transforms an arbitrary number of input bytes into a (typically) fixed-size output; common examples of hash functions are `MD5`, and `SHA-256`.**

# Rainbow tables
---
- Rainbow tables are large **pre-compiled maps of input and output values for a given hash function.** 
- These can be **used to very quickly identify the password if its corresponding hash has already been mapped.**
	- ![[Pasted image 20250628075154.png]]
- Because rainbow tables are such a powerful attack, `salting` is used.

## Salts
---
- A `salt`, in cryptographic terms, is a **random sequence of bytes added to a password before it is hashed.** 
- To maximize impact, **salts should NOT be reused,** e.g. for all passwords stored in one database.
- A salt is not a secret value — when a system goes to check an authentication request, **it needs to know what salt was used so that it can check if the password hash matches.**
- For this reason, **salts are typically prepended to corresponding hashes**. 
- The reason this technique works against rainbow tables is that **even if the correct password has been mapped, the combination of salt and password has likely not.**
- ![[Pasted image 20250628075431.png]]

# Brute-force attack
---
- A `brute-force` attack involves **attempting every possible combination of letters, numbers, and symbols until the correct password is discovered.**
- Obviously, this can take a very long time—especially for long passwords—however shorter passwords (<9 characters) are viable targets, even on consumer hardware.

# Dictionary attack
---
- A `dictionary` attack, otherwise known as a `wordlist` attack, is **one of the most `efficient` techniques for cracking passwords,** especially when operating under time-constraints as penetration testers usually do.
- Well-known wordlists for password cracking are [rockyou.txt](https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt) and those included in [SecLists](https://github.com/danielmiessler/SecLists).