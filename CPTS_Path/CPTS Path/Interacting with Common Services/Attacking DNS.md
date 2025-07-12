- The [Domain Name System](https://www.cloudflare.com/learning/dns/what-is-dns/) (`DNS`) translates domain names (e.g., hackthebox.com) to the numerical IP addresses (e.g., 104.17.42.72).
- DNS is **mostly `UDP/53`, but DNS will rely on `TCP/53`** more heavily as time progresses.

# Enumeration
---
[[DNS]]
- `nmap -p53 -Pn -sV -sC 10.10.110.213`

# DNS Zone Transfer
----
- [[DNS Zone Transfers]]
- Unless a DNS server is configured correctly (limiting which IPs can perform a DNS zone transfer), anyone can ask a DNS server for a copy of its zone information since DNS zone transfers do not require any authentication. 
- For exploitation, **==we can use the `dig` utility with DNS query type `AXFR` option to dump the entire DNS==** namespaces from a vulnerable DNS server:

	- `dig AXFR @ns1.inlanefreight.htb inlanefreight.htb`

- Tools like **==[Fierce](https://github.com/mschwager/fierce) can also be used to enumerate all DNS servers of the root domain and scan for a DNS zone transfer==**:
	- `fierce --domain zonetransfer.me`

# Domain Takeovers & Subdomain Enumeration
---
- `Domain takeover` is **registering a non-existent domain name to gain control over another domain.** 
- If attackers find an **expired domain, they can claim that domain to perform further attacks such as hosting malicious content** on a website or sending a phishing email leveraging the claimed domain.
- Domain takeover is **also possible with subdomains called `subdomain takeover`**
- A DNS's **==canonical name (`CNAME`) record is used to map different domains to a parent domain.==**
- Many organizations use third-party services like AWS, GitHub, Akamai, Fastly, and other content delivery networks (CDNs) to host their content.

![[Pasted image 20250712060137.png]]
- The domain name (e.g., `sub.target.com`) uses a **CNAME record to another domain** (e.g., `anotherdomain.com`).
- Suppose the **`anotherdomain.com` expires and is available for anyone to claim the domain** since the `target.com`'s DNS server has the `CNAME` record.
	- In that case, **==anyone who registers `anotherdomain.com` will have complete control over `sub.target.com`==** until the DNS record is updated.

## Subdomain Enumeration
---
- Before performing a subdomain takeover, **we should enumerate subdomains for a target domain** using tools like [Subfinder](https://github.com/projectdiscovery/subfinder).
- This tool can scrape subdomains from open sources like [DNSdumpster](https://dnsdumpster.com/). 
- Other tools like [Sublist3r](https://github.com/aboul3la/Sublist3r) can also be used to brute-force subdomains by supplying a pre-generated wordlist:
	- `./subfinder -d inlanefreight.com -v`

- An **==excellent alternative is a tool called [Subbrute](https://github.com/TheRook/subbrute).==**
- This tool allows us to use self-defined resolvers and perform pure DNS brute-forcing attacks during internal penetration tests on hosts that do not have Internet access.

	- `echo "ns1.inlanefreight.com" > ./resolvers.txt`
	- `./subbrute inlanefreight.com -s ./names.txt -r ./resolvers.txt`

- Using the `nslookup` or `host` command, **we can enumerate the `CNAME` records for found subdomains.**:
	- `host support.inlanefreight.com`
- The URL `https://support.inlanefreight.com` shows a `NoSuchBucket` error indicating that the subdomain is potentially vulnerable to a subdomain takeover.
- The **[can-i-take-over-xyz](https://github.com/EdOverflow/can-i-take-over-xyz) repository is also an excellent reference for a subdomain takeover vulnerability.**

# DNS Spoofing
---
- DNS spoofing is also referred to as **DNS Cache Poisoning**. 
- This attack involves **==altering legitimate DNS records with false information==** so that they can be used to **redirect online traffic to a fraudulent website.**
- Poisoning are as follows:
	- An attacker coul**d intercept the communication between a user and a DNS server** to route the user to a fraudulent destination instead of a legitimate one by **performing a Man-in-the-Middle (`MITM`) attack.**	   
	- **Exploiting a vulnerability found in a DNS server could yield control over the server by an attacker to modify the DNS records.**

## Local DNS Cache Poisoning
- From a **local network** perspective, an attacker can also **perform DNS Cache Poisoning using MITM tools like [Ettercap](https://www.ettercap-project.org/) or [Bettercap](https://www.bettercap.org/).**
- To exploit the DNS cache poisoning via `Ettercap`, **we should first edit the `/etc/ettercap/etter.dns` file to map the target domain name** (e.g., `inlanefreight.com`) **that they want to spoof** **and the attacker's IP address (e.g., `192.168.225.110`) that they want to redirect a user to:**
		![[Pasted image 20250712060850.png]]

- Next, start the `Ettercap` tool and scan for live hosts within the network by navigating to `Hosts > Scan for Hosts`. 
- Once completed, add the target IP address (e.g., `192.168.152.129`) to Target1 and add a default gateway IP (e.g., `192.168.152.2`) to Target2.
	- ![[Pasted image 20250712060925.png]]
- Activate `dns_spoof` attack by navigating to `Plugins > Manage Plugins`. This sends the target machine with fake DNS responses that will resolve `inlanefreight.com` to IP address `192.168.225.110`:
	- ![[Pasted image 20250712060946.png]]
- After a successful DNS spoof attack, if a victim user coming from the target machine `192.168.152.129` visits the `inlanefreight.com` domain on a web browser, they will be redirected to a `Fake page` that is hosted on IP address `192.168.225.110`:
	- ![[Pasted image 20250712061000.png]]
- In addition, a ping coming from the target IP address `192.168.152.129` to `inlanefreight.com` should be resolved to `192.168.225.110` as well

# Labs
---
=>  Find all available DNS records for the "inlanefreight.htb" domain on the target name server and submit the flag found as a DNS record as the answer. 
- Add Domain Name Server to resolvers file:
	- `echo "10.129.83.215" > ./resolvers.txt`
- Run `subbrute.py`:
	- `./subbrute.py inlanefreight.htb -s ./names.txt -r ./resolvers.txt `
- `dig AXFR @10.129.83.215 hr.inlanefreight.htb`
	- ![[Pasted image 20250712064227.png]]