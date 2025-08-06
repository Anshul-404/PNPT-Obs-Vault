# Port Forwarding in Context
---
 - `Port forwarding` is a technique that allows us to **redirect a communication request from one port to another.**
 - Port forwarding uses TCP as the primary communication layer to provide interactive communication for the forwarded port.
 - Different **application layer protocols such as SSH or even [SOCKS](https://en.wikipedia.org/wiki/SOCKS)** (non-application layer) can be used to **encapsulate the forwarded traffic.**


# SSH Local Port Forwarding
---
- Port is forwarded **LOCALLY i.e on the same machine.**
- In this scenario, **port 3306 is forwarded to port 22 on the same victim machine**
![[Pasted image 20250716063445.png]]
- We have our **attack host (10.10.15.x) and a target Ubuntu server (10.129.x.x)**, which we have compromised.
- The **`-L` command** tells the SSH client to request the SSH server to **forward all the data we send via the port `1234` to `localhost:3306`** on the Ubuntu server.
	- **==attacker:1234 <-> localhost:22 <-> localhost:3306==**
	  
- Executing the Local Port Forward:
	- `ssh -L 1234:localhost:3306 ubuntu@10.129.202.64`
- Forwarding Multiple Ports:
	- `ssh -L 1234:localhost:3306 -L 8080:localhost:80 ubuntu@10.129.202.64`

# Enabling Dynamic Port Forwarding with SSH
---
- Unlike the previous scenario where we knew which port to access, in our current scenario, **we don't know which services lie on the other side of the network.**
- We cannot perform scan directly **from our attack host because it does not have routes to the `172.16.5.0/23` network.**
- To do this, **we will have to perform `dynamic port forwarding` and `pivot`** our network packets via the Ubuntu server.
- We can do this by **starting a `SOCKS listener` on our `local host` (personal attack host)** and then configure SSH to **forward that traffic via SSH to the network (172.16.5.0/23) after connecting to the target host**.
- This is called **`SSH tunneling` over `SOCKS proxy`.**
- This technique is often used to **circumvent the restrictions put in place by firewalls**, and allow an external entity to bypass the firewall and access a service within the firewalled environment.
- **SOCKS4 doesn't provide any authentication and UDP support, whereas SOCKS5 does provide that.**
- ![[Pasted image 20250716064407.png]]

- Enabling Dynamic Port Forwarding with SSH:
	- `ssh -D 9050 ubuntu@10.129.202.64`
- The `-D` argument requests the SSH server to enable dynamic port forwarding.
- . Once we have this enabled, **we will require a tool that can route any tool's packets over the port `9050`.** 
- We can do this u**sing the tool `proxychains`**, which is capable of redirecting TCP connections through TOR, SOCKS, and HTTP/HTTPS proxy servers.
- Proxychains is often used to force an application's `TCP traffic` to go through hosted proxies like `SOCKS4`/`SOCKS5`, `TOR`, or `HTTP`/`HTTPS` proxies.
- To inform proxychains that we must use port 9050, **we must modify the proxychains configuration file located at `/etc/proxychains.conf`**. We **can add `socks4 127.0.0.1 9050` to the last line** if it is not already there.

- Running commands:
	- `proxychains nmap -v -sn 172.16.5.1-200`
- One more important note to remember here is that w**e can only perform a `full TCP connect scan` over proxychains.**
- The reason for this is that **proxychains cannot understand partial packets**. If you send partial packets like half connect scans, **it will return incorrect results.**:
	- `proxychains nmap -v -Pn -sT 172.16.5.19`
	- `proxychains msfconsole`