# SSH
---
- Secure Shell (SSH) enables two computers to establish an encrypted and direct connection within a possibly insecure network on the **standard port TCP 22**
- SSH-2, also known as SSH version 2, is a more advanced protocol than SSH version 1 in encryption, speed, stability, and security. For example, SSH-1 is vulnerable to MITM attacks, whereas SSH-2 is not.
- Authentication Methods:
	- Password authentication
	- Public-key authentication
	- Host-based authentication
	- Keyboard authentication
	- Challenge-response authentication
	- GSSAPI authentication

- **Public Key Authentication**: 
---
**Generate Public and Private key on Client and copy public key on server**

🔹 Step 1: Server Checks for Public Key
    Server looks in ~/.ssh/authorized_keys for your public key.
🔹 Step 2: Challenge
    If it finds your public key, it creates a random challenge string (like a nonce) and encrypts it using your public key.
🔹 Step 3: Response
    Server sends the encrypted challenge to your client.
    Your client decrypts it using your private key.
🔹 Step 4: Verification
    The decrypted string is sent back to the server.
    If the decrypted value matches what the server originally sent, authentication succeeds — this proves you own the private key.

# Default Configuration
---
- The sshd_config file, responsible for the OpenSSH server, has only a few of the settings configured by default.
- The default configuration **includes X11 forwarding**, which contained a command injection vulnerability in **==version 7.2p1 of OpenSSH in 2016.==** 
- `/etc/ssh/sshd_config `

# Dangerous Settings
---
![[Pasted image 20250618060644.png]]

# Footprinting - ssh
---
- **ssh-audit**
	- `git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit`
	- `./ssh-audit.py 10.129.14.132`
	- The first thing we can see in the **==first few lines of the output is the banner==** that reveals the version of the OpenSSH server. 
	- The previous versions had some vulnerabilities, such as **CVE-2020-14145**, which allowed the attacker the capability to **Man-In-The-Middle** and attack the initial connection attempt. 

# Change Authentication Method
---
- For potential brute-force attacks, **we can specify the authentication method** with the SSH client option `PreferredAuthentications`:
	- `ssh -v cry0l1t3@10.129.14.132 -o PreferredAuthentications=password`

- With SSH-1.99-OpenSSH_3.9p1, we know that we can use both protocol versions SSH-1 and SSH-2, and we are dealing with OpenSSH server version 3.9p1. 
- On the other hand, for a banner with SSH-2.0-OpenSSH_8.2p1, we are dealing with an OpenSSH version 8.2p1 which only accepts the SSH-2 protocol version.
# Rsync
---
- Rsync is a fast and efficient tool for locally and remotely copying files.
- By default, it uses **port 873** and can be configured to use SSH for secure file transfers by piggybacking on top of an established SSH server connection.
- This [guide](https://book.hacktricks.xyz/network-services-pentesting/873-pentesting-rsync) covers some of the ways Rsync can be abused, most notably **by listing the contents of a shared folder on a target server and retrieving files.**
- We will need credentials. If you find **credentials** during a pentest and run into Rsync on an internal (or external) host, it is always worth **checking for password re-use**
- Scanning for Rsync:
	- `nmap -sV -p 873 127.0.0.1`
- Probing for Accessible Shares
	- `nc -nv 127.0.0.1 873`
- Enumerating an Open Share:
	- `rsync -av --list-only rsync://127.0.0.1/dev`
- We could **sync all files to our attack host** with the command `rsync -av rsync://127.0.0.1/dev`.
- If Rsync is configured to use SSH to transfer files, we could modify our **commands to include the `-e ssh` flag, or `-e "ssh -p2222"` if a non-standard port is in use for SSH.**

# R-Services
---
- R-Services are a **suite of services hosted to enable remote access** or issue **commands between Unix hosts** over TCP/IP. 
- Much like `telnet`, r-services transmit information from client to server(and vice versa.) **over the network in an unencrypted format,** making it possible for **attackers to intercept network traffic**
- `R-services` span across the **ports `512`, `513`, and `514`**
- ![[Pasted image 20250618061558.png]]
- Abused Commands:
	- ![[Pasted image 20250618061614.png]]
- `/etc/hosts.equiv`:
	- Contains a **list of trusted hosts and is used to grant access to other systems on the network.** When users on one of these hosts attempt to access the system, **==they are automatically granted access without further authentication.==**
	- `# <hostname> <local username>
	  `pwnbox cry0l1t3`

# Footprinting - Scanning for R-Services
---
- `nmap -sV -p 512,513,514 10.0.17.2`

# Access Control & Trusted Relationships
---
- The primary concern for r-services, and one of the primary reasons SSH was introduced to replace it, is the inherent issues regarding access control for these protocols.
- R-services rely on trusted information sent from the remote client to the host machine they are attempting to authenticate to.
- By default, these services utilize [Pluggable Authentication Modules (PAM)](https://debathena.mit.edu/trac/wiki/PAM) for user authentication onto a remote system; **however, they also bypass this authentication through the use of the `/etc/hosts.equiv` and `.rhosts` files on the system.**
- The `hosts.equiv` and `.rhosts` files contain a list of hosts (`IPs` or `Hostnames`) and users that are `trusted` by the local host
- **Note:** The `hosts.equiv` file is recognized as the global configuration regarding all users on a system, whereas `.rhosts` provides a per-user configuration.
- ![[Pasted image 20250618061932.png]]
- The `+` modifier allows **any external user to access r-commands from the `htb-student` user account** via the host with the IP address `10.0.17.10`.

# Logging in Using Rlogin
---
`rlogin 10.0.17.2 -l htb-student`
- We have successfully logged in under the `htb-student` account on the **remote host due to the misconfigurations in the `.rhosts` file.**

# Listing Authenticated Users Using Rwho
---
`rwho`
# Listing Authenticated Users Using Rusers
---
`rusers -al 10.0.17.5`
- This will give us a **more detailed account of all logged-in users** over the network, including information such as the **username, hostname of the accessed machine, TTY that the user is logged in to, the date and time the user logged in,** the amount of time since the user typed on the keyboard, and the remote host they logged in from (if applicable).