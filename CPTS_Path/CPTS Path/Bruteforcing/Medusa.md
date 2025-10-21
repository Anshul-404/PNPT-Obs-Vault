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