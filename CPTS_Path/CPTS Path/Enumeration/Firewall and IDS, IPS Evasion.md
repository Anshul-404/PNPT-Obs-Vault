	# Firewalls
---
- Nmap's TCP ACK scan (`-sA`) method is much harder to filter for firewalls and IDS/IPS systems than regular SYN (`-sS`) or Connect scans (`sT`) because they only send a TCP packet with only the `ACK` flag.
- When a port is closed or open, the host must respond with an `RST` flag.
- Unlike outgoing connections, all connection attempts (with the `SYN` flag) from external networks are usually blocked by firewalls.
- The packets with the `ACK` flag are often passed by the firewall because the firewall cannot determine whether the connection was first established from the external network or the internal network.
	- ` nmap 10.129.2.28 -p 21,22,25 -sA -Pn -n --disable-arp-ping --packet-trace`

# IPS / IDS
---
- `IPS systems` take measures configured by the administrator independently to prevent potential attacks automatically. It is essential to know that IDS and IPS are different applications and that IPS serves as a complement to IDS.
- We can trigger certain security measures from an administrator, for example, by aggressively scanning a single port and its service. Based on whether specific security measures are taken, we can detect if the network has some monitoring applications or not.

- **Decoys**
---
- There are cases in which administrators block specific subnets from different regions in principle. This prevents any access to the target network.
- For this reason, the Decoy scanning method (`-D`) is the right choice. With this method, Nmap generates various random IP addresses inserted into the IP header to disguise the origin of the packet sent.
- With this method, we can generate random (`RND`) a specific number (for example: `5`) of IP addresses separated by a colon (`:`).
- Another critical point is that the decoys must be alive. Otherwise, the service on the target may be unreachable due to SYN-flooding security mechanisms.
- Our real IP address is then randomly placed between the generated IP addresses.
	- `nmap 10.129.2.28 -p 80 -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5`

- Another scenario would be that only individual subnets would not have access to the server's specific services. So we can also manually specify the source IP address (`-S`) to test if we get better results with this one.
	- `nmap 10.129.2.28 -n -Pn -p 445 -O -S 10.129.2.200 -e tun0`


# DNS Proxying
---
- By default, `Nmap` performs a reverse DNS resolution unless otherwise specified to find more important information about our target.
- However, `Nmap` still gives us a way to specify DNS servers ourselves (`--dns-server <ns>,<ns>`). 
- This method could be fundamental to us if we are in a demilitarized zone (`DMZ`).
- The company's DNS servers are usually more trusted than those from the Internet.
- We can use `TCP port 53` as a source port (`--source-port`) for our scans. If the administrator uses the firewall to control this port and does not filter IDS/IPS properly, our TCP packets will be trusted and passed through.
	- `nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace --source-port 53`

- Connecting to filtered port using:
	- `ncat -nv --source-port 53 10.129.133.173 50000`