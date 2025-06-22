# Common Stuff (Skipping Known Stuff)
----
- TCP Scan: `nmap -sS localhost`
- All Ports Scan: `nmap -p- localhost`
- Host discover: ` nmap 10.129.2.0/24 -sn `, `nmap -sn -oA tnet -iL ip_list.lst`
- Nmap Scripts:
	- Default Scripts: `nmap <target> -sC`
	- Specific Scripts Category: `nmap <target> --script <category>`
	- Defined Scripts: `nmap <target> --script <script-name>,<script-name>,...`
		- `nmap 10.129.2.28 -p 25 --script banner,smtp-commands`
- Aggressive Scan: `nmap 10.129.2.28 -p 80 -A`
- Vulnerability Assessment: `nmap 10.129.2.28 -p 80 -sV --script vuln `


# Performance
---
**==Aaccelerating can also have a negative effect on our results, which means we can overlook important information.==**

- Timeouts: When Nmap sends a packet, **it takes some time (Round-Trip-Time - RTT)** to receive a response from the scanned port. 
	- `sudo nmap 10.129.2.0/24 -F --initial-rtt-timeout 50ms --max-rtt-timeout 100ms`
		- `--initial-rtt-timeout 50ms` 	: Sets the specified time value as initial RTT timeout.
		- `--max-rtt-timeout 100ms` :	Sets the specified time value as maximum RTT timeout.
- Max Retries: Another way to increase scan speed is by specifying the retry rate of sent packets **(--max-retries).** **The default value is 10, but we can reduce it to 0.** This means if **Nmap does not receive a response for a port, it won't send any more packets to that port and will skip it.**
	- `nmap 10.129.2.0/24 -F --max-retries 0`
- Rates:  If we know the network bandwidth, **we can work with the rate of packets sent**, which significantly speeds up our scans with Nmap. 
- When setting the **minimum rate (--min-rate \<number\>)** for sending packets, we tell Nmap to simultaneously send the specified number of packets. It will attempt to maintain the rate accordingly.
	- `nmap 10.129.2.0/24 -F -oN tnet.minrate300 --min-rate 300`
- Timing: These values (0-5) determine the aggressiveness of our scans. This can also have n**egative effects if the scan is too aggressive,** and security systems may **block us due to the produced network traffic.** 
```
    -T 0 / -T paranoid
    -T 1 / -T sneaky
    -T 2 / -T polite
    -T 3 / -T normal
    -T 4 / -T aggressive
    -T 5 / -T insane
```