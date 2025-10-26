 Medusa, a prominent tool in the cybersecurity arsenal, is **designed to be a fast, massively parallel, and modular login brute-forcer.**

# Command Syntax and Parameter Table
---
- `medusa [target_options] [credential_options] -M module [module_options]`

# Targeting an SSH Server
---
	- `medusa -h 192.168.0.100 -U usernames.txt -P passwords.txt -M ssh`
	- Employ the ssh module for the attack

# Targeting Multiple Web Servers with Basic HTTP Authentication
---
- `medusa -H web_servers.txt -U usernames.txt -P passwords.txt -M http -m GET`
	- Employ the **`http` module** with the **`GET` method** to attempt logins.

# Testing for Empty or Default Passwords
---
- If you want to assess **whether any accounts on a specific host (`10.0.0.5`) have empty or default passwords** (where the password matches the username), you can use:
	- `medusa -h 10.0.0.5 -U usernames.txt -e ns -M service_name`
	- Perform **additional checks for empty passwords** (`-e n`) **and passwords matching the username** (`-e s`).

# Web Services
----
- The following command serves as our starting point:
	- `medusa -h <IP> -n <PORT> -u sshuser -P 2023-200_most_used_passwords.txt -M ssh -t 3`
	
	-  `-h <IP>`: Specifies the target system's IP address.
	- `-n <PORT>`: Defines the port on which the SSH service is listening (typically port 22).
	- `-u sshuser`: Sets the username for the brute-force attack.
	- `-P 2023-200_most_used_passwords.txt`: Points Medusa to a wordlist containing the 200 most commonly used passwords in 2023. The effectiveness of a brute-force attack is often tied to the quality and relevance of the wordlist used.
	- `-M ssh`: Selects the SSH module within Medusa, tailoring the attack specifically for SSH authentication.
	- `-t 3`: Dictates the number of parallel login attempts to execute concurrently. Increasing this number can speed up the attack but may also increase the likelihood of detection or triggering security measures on the target system.
	- `medusa -h IP -n PORT -u sshuser -P 2023-200_most_used_passwords.txt -M ssh -t 3`

## Targeting the FTP Server
---
If we explore the `/home` directory on the target system, we see an `ftpuser` folder, which implies the likelihood of the FTP server username being `ftpuser`. Based on this, we can modify our Medusa command accordingly:
- ` medusa -h 127.0.0.1 -u ftpuser -P 2020-200_most_used_passwords.txt -M ftp -t 5`