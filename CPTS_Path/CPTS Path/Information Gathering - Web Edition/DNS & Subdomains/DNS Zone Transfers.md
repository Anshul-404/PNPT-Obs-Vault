[[DNS]]
- While brute-forcing can be a fruitful approach, there's a less invasive and potentially more efficient method for uncovering subdomains – DNS zone transfers. 
- This mechanism, **==designed for replicating DNS records between name servers, can inadvertently become a goldmine of information for prying eyes if misconfigured.==**

# What is a Zone Transfer?
---
- A DNS zone transfer is essentially a wholesale **copy of all DNS records within a zone** (a domain and its subdomains) from one name server to another.
- This process is essential for maintaining consistency and redundancy across DNS servers.
- However, **==if not adequately secured, unauthorised parties can download the entire zone file, revealing a complete list of subdomains, their associated IP addresses, and other sensitive DNS data.==**
- **Zone Transfer Request (AXFR)**: The secondary DNS server initiates the process by s**ending a zone transfer request to the primary server**. This request typically uses the AXFR (Full Zone Transfer) type.
- SOA Record Transfer: Upon receiving the request (and potentially authenticating the secondary server), the primary server responds by sending its Start of Authority (SOA) record.
	- he SOA record contains vital information about the zone, including its serial number, which helps the secondary server determine if its zone data is current.

# The Zone Transfer Vulnerability
---
- While zone transfers are essential for legitimate DNS management, a **misconfigured DNS server can transform this process into a significant security vulnerability.** The core issue lies in the access controls governing who can initiate a zone transfer.
- The information gleaned from an unauthorised zone transfer can be invaluable to an attacker. It reveals a comprehensive map of the target's DNS infrastructure, including:
    **Subdomains**: A complete list of subdomains, many of which might not be linked from the main website or easily discoverable through other means. These hidden subdomains could host development servers, staging environments, administrative panels, or other sensitive resources.
    **IP Addresses**: The IP addresses associated with each subdomain, providing potential targets for further reconnaissance or attacks.
    **Name Server Records**: Details about the authoritative name servers for the domain, revealing the hosting provider and potential misconfigurations.

# Exploitation
---
- `dig axfr @nsztm1.digi.ninja zonetransfer.me`
- This command instructs `dig` to request a full zone transfer (`axfr`) from the DNS server responsible for `zonetransfer.me`. If the server is misconfigured and allows the transfer, you'll receive a complete list of DNS records for the domain, including all subdomains.

# Lab
---
=> After performing a zone transfer for the domain inlanefreight.htb on the target system, how many DNS records are retrieved from the target system's name server? Provide your answer as an integer, e.g, 123.
- `dig AXFR inlanefreight.htb @10.129.131.180`