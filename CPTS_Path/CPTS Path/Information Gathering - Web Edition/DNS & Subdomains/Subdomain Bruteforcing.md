- Subdomain Brute-Force Enumeration is a powerful active subdomain discovery technique that leverages pre-defined lists of potential subdomain names. 

# Steps
---
1. **Wordlist Selection**: The process begins with selecting a **wordlist containing potential subdomain names** . These wordlists can be:
	- General-Purpose: Containing a broad range of common subdomain names (e.g., `dev`, `staging`, `blog`, `mail`, `admin`, `test`). This approach is ==**useful when you don't know the target's naming conventions.**== -> (subdomains-top1million-110000.txt)
	- `Targeted`: Focused on specific industries, technologies, or naming patterns relevant to the target. **This approach is more efficient and reduces the chances of false positives.**
	- `Custom`: You can create your own wordlist based on specific **keywords, patterns, or intelligence gathered from other sources.** -> (cewl)
2. `Iteration and Querying`: A script or tool iterates through the wordlist, appending each word or phrase to the main domain (e.g., `example.com`) to create potential subdomain names (e.g., `dev.example.com`, `staging.example.com`).
3. `DNS Lookup`: A DNS query is performed for each potential subdomain to check if it resolves to an IP address. This is typically done using the A or AAAA record type.
4. `Filtering and Validation`: If a subdomain resolves successfully, it's added to a list of valid subdomains. Further validation steps might be taken to confirm the subdomain's existence and functionality (e.g., by attempting to access it through a web browser).


# Tools - DNSEnum
---
![[Pasted image 20250620060636.png]]
- `dnsenum` is a versatile and widely-used command-line tool written in Perl. It is a comprehensive toolkit for DNS reconnaissance, providing various functionalities to gather information about a target domain's DNS infrastructure and potential subdomains
	- ![[Pasted image 20250620060705.png]]
- Command:
	- `dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -r`
		- `-r`-> recurse (subdomains of subdomain.)