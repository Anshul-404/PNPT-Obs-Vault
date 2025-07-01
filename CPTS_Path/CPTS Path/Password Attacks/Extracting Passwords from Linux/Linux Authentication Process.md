- Linux-based distributions support **various authentication mechanisms.** 
- One of the **most commonly used is [Pluggable Authentication Modules (PAM)](https://web.archive.org/web/20220622215926/http://www.linux-pam.org/Linux-PAM-html/Linux-PAM_SAG.html).**
- The modules responsible for this functionality, **such as `pam_unix.so` or `pam_unix2.so`, are typically located in `/usr/lib/x86_64-linux-gnu/security/`** on Debian-based systems.
- These modules **==manage user information, authentication, sessions, and password changes==**
- The **`pam_unix.so` module uses standardized API calls** from system libraries to update account information. 
- **The primary files it reads from and writes to are `/etc/passwd` and `/etc/shadow`**

# Passwd file
---
- The `/etc/passwd` file contains information about every user on the system and is readable by all users and services.
- . Each entry in the file corresponds to a single user and consists of `seven fields`, which store user-related data in a structured format.
- ![[Pasted image 20250701061258.png]]
- In rare cases (generally on very old systems) ==**password field may hold the actual password hash**==
- Usually, **we will find the value `x` in this field, indicating that the passwords are stored in a hashed form within the `/etc/shadow` file.**
- However, it can also be that the **`/etc/passwd` file is ==writeable== by mistake.** This would **==allow us to remove the password field for the `root` user entirely.==**
- This results in no password prompt being displayed when attempting to log in as `root`.

# Shadow file
---
- It contains **==all password information for created users.==** 
- For example, **if there is no entry in the `/etc/shadow` file for a user listed in `/etc/passwd`, that user is considered invalid.**
- ![[Pasted image 20250701061536.png]]
- If the `Password` **==field contains a character such as `!` or `*`, the user cannot log in using a Unix password==**. 
- However, other authentication methods—**such as Kerberos or key-based authentication—can still be used.**
- The same applies **if the `Password` field is empty, meaning no password is required for login.**
	- ![[Pasted image 20250701061636.png]]

# ==Opasswd==
---
- The PAM library (`pam_unix.so`) ==**can prevent users from reusing old passwords.**==
- These **==previous passwords are stored in the `/etc/security/opasswd` file.==** Administrator (root) privileges are required to read this file, assuming its permissions have not been modified manually.

# Cracking Linux Credentials
---
- To do this, we can use a **tool called [unshadow](https://github.com/pmittaldev/john-the-ripper/blob/master/src/unshadow.c)**, which is included with John the Ripper (JtR). 
- **It works by combining the `passwd` and `shadow` files into a single file suitable for cracking.**
- ![[Pasted image 20250701062053.png]]
- `hashcat -m 1800 -a 0 /tmp/unshadowed.hashes rockyou.txt -o /tmp/unshadowed.cracked`