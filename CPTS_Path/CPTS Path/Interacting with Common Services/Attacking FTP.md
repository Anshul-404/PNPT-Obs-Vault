- The [File Transfer Protocol](https://en.wikipedia.org/wiki/File_Transfer_Protocol) (`FTP`) is a standard network protocol used to transfer files between computers. It also performs directory and files operations, such as changing the working directory, listing files, and renaming and deleting directories or files. **By default, FTP listens on port `TCP/21`.**

# Enumeration
---
- [[FTP]]
- `Nmap` default scripts **`-sC` includes the [ftp-anon](https://nmap.org/nsedoc/scripts/ftp-anon.html) Nmap script which checks if a FTP server allows anonymous logins.**
- The version enumeration flag `-sV` provides interesting information about **FTP services, such as the FTP banner,** which often includes the version name.
- We can use the **`ftp` client or `nc` to interac**t with the FTP service. By default, FTP runs on TCP port 21.


## Nmap
---
- `sudo nmap -sC -sV -p 21 192.168.2.142`

# Misconfigurations
---
- To access with anonymous login, **we can use the `anonymous` username and no password.**
- This will be **dangerous** for the company if **read and write permissions have not been set up correctly for the FTP service.**
- Using other vulnerabilities, **such as path traversal in a web application**, we **would be able to find out where this file is located and execute it as PHP code, for example.**
	- ![[Pasted image 20250707075055.png]]

# Protocol Specifics Attacks
---
## Brute Forcing
- If there is no anonymous authentication available, **we can also brute-force the login for the FTP services** using a list of the pre-generated usernames and passwords.
- **With `Medusa`**, we can use the option `-u` to specify a single user to target, or you can use the option `-U` to provide a file with a list of usernames. 
- The option `-P` is for a file containing a list of passwords. 
- We can use the option `-M` and the protocol we are targeting (FTP) and the option `-h` for the target hostname or IP address.

- **Brute Forcing with Medusa**:
	- `medusa -u fiona -P /usr/share/wordlists/rockyou.txt -h 10.129.203.7 -M ftp `

## ==FTP Bounce Attack==
- An FTP bounce attack is a network attack that uses **FTP servers to deliver outbound traffic to another device on the network.**
- The attacker **==uses a `PORT` command to trick the FTP connection into running commands and getting information from a device other than the intended server.==**
- Consider **we are targetting an FTP Server `FTP_DMZ` exposed to the internet.** Another device within the same network, `Internal_DMZ`, is not exposed to the internet. **==We can use the connection to the `FTP_DMZ` server to scan `Internal_DMZ` using the FTP Bounce attack==** and obtain information about the server's open ports.

- The **==`Nmap` -b flag can be used to perform an FTP bounce attack:==**
	- `nmap -Pn -v -n -p80 -b anonymous:password@10.10.110.213 172.17.0.2`
- ![[Pasted image 20250707075608.png]]

# Labs
---
- Medusa takes soooo long, just use good old frend hydra :):
	- `hydra -L users.list -P passwords.list  ftp://10.129.203.6:2121 -I`

- ![[Pasted image 20250707080626.png]]