@ -> Which domain server to use
Without `@`, `dig` would use whatever DNS server is listed in your `/etc/resolv.conf` (usually your local or ISP's resolver), and they **won’t allow AXFR** or know anything internal.
- _If a subdomain isn't in my brute-force list, zone transfer might still give it to me._
# Components
---
![[Pasted image 20250615205602.png]]
- By default, IT security professionals apply DNS over TLS (DoT) or DNS over HTTPS (DoH) here. In addition, the network protocol DNSCrypt also encrypts the traffic between the computer and the name server.
# DIG queries
---
![[Pasted image 20250615210145.png]]

# Local DNS Configuration
---
- `cat /etc/bind/named.conf.local`

# Dangerous Settings
---
- A list of vulnerabilities targeting the **BIND9 server can be found at [CVEdetails](https://www.cvedetails.com/product/144/ISC-Bind.html?vendor_id=64)**. In addition, SecurityTrails provides a ==**short [list](https://web.archive.org/web/20250329174745/https://securitytrails.com/blog/most-popular-types-dns-attacks) of the most popular attacks on DNS servers.**==
- ![[Pasted image 20250615210514.png]]

# ==Zone Transfers== - Master / Slave sync of records
---
- `Zone transfer` refers to the **==transfer of zones to another server in DNS==**, which generally happens over TCP port 53. 
- This procedure is abbreviated `Asynchronous Full Transfer Zone` (`AXFR`).
- Synchronization between the servers involved is realized by zone transfer.
- Using a **secret key `rndc-key`,** which we have seen initially in the default configuration, the servers **==make sure that they communicate with their own master or slave.==**
- The **slave fetches the `SOA` record of the relevant zone from the master at certain intervals,** the so-called refresh time, usually one hour, and compares the serial numbers
	- `dig axfr inlanefreight.htb @10.129.14.128`
	- Internal: `dig axfr internal.inlanefreight.htb @10.129.14.128`
- If the administrator used a ==**subnet for the `allow-transfer` option**== for testing purposes or as a workaround solution or ==**set it to `any`, everyone would query the entire zone file at the DNS server.**==
# Footprinting
---
- `dig ns inlanefreight.htb @10.129.14.128`
- `dig CH TXT version.bind 10.129.120.85`
- `dig any inlanefreight.htb @10.129.14.128`
- `dig axfr inlanefreight.htb @10.129.14.128`

# Sub Domain Bruteforcing
---
- `for sub in $(cat /opt/useful/seclists/Discovery/DNS/subdomains-top1million-110000.txt);do dig $sub.inlanefreight.htb @10.129.14.128 | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done`
- Using DNSEnum:
		- `dnsenum --dnsserver 10.129.14.128 --enum -p 0 -s 0 -o subdomains.txt -f /opt/useful/seclists/Discovery/DNS/subdomains-top1million-110000.txt inlanefreight.htb`

# Lab Answers
---
=>  Interact with the target DNS using its IP address and enumerate the FQDN of it for the "inlanefreight.htb" domain. 
- `dnsenum --dnsserver 10.129.141.193 --enum -p 0 -s 0 -o subdomains.txt -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt inlanefreight.htb --threads 1000`
	- `ns.inlanefreight.htb`
	- ![[Pasted image 20250616044749.png]]

=> Identify if its possible to perform a zone transfer and submit the TXT record as the answer. (Format: HTB{...}) 
- `dig AXFR internal.inlanefreight.htb @10.129.141.193`
- `HTB{DN5_z0N3_7r4N5F3r_iskdufhcnlu34}`
	- ![[Pasted image 20250616044922.png]]

=> What is the FQDN of the host where the last octet ends with "x.x.x.203"?

`dnsenum --dnsserver 10.129.141.193 --enum -p 0 -s 0 -o subdomains.txt -f /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt dev.inlanefreight.htb --threads 1000`
![[Pasted image 20250616050507.png]]
