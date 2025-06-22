- Once the DNS directs traffic to the correct server, the web server configuration becomes crucial in determining how the incoming requests are handled.
- Web servers like Apache, Nginx, or IIS are designed to **host multiple websites or applications on a single server.** 
- They achieve this through virtual hosting, which allows them to **differentiate between domains, subdomains, or even separate websites with distinct content.**

# VHosts vs Subdomains
---
- Subdomains: 
	- These are **extensions of a main domain name** (e.g., blog.example.com is a subdomain of example.com). 
	- **Subdomains typically have their own DNS records, pointing to either the same IP address as the main domain or a different one.** They can be used to organise different sections or services of a website.
	
- Virtual Hosts (VHosts):  (Can have different domain names)
	- Virtual hosts are configurations within a web server that allow **multiple websites or applications to be hosted on a single server.** 
	- They can be associated with top-level domains (e.g., example.com) or subdomains (e.g., dev.example.com). 
	- **Each virtual host can have its own separate configuration,** enabling precise control over how requests are handled.
	- If a virtual host does not have a DNS record, you can still access it by modifying the `hosts` file on your local machine. The `hosts` file allows you to map a domain name to an IP address manually, bypassing DNS resolution.
	- Websites often have subdomains t**hat are not public and won't appear in DNS records.** 
	- `VHost fuzzing` is a technique to ==**discover public and non-public `subdomains` and `VHosts` by testing various hostnames against a known IP address.**==
		![[Pasted image 20250620070618.png]]

# Vhost Procedure
---
- **Browser Requests a Website**: When you enter a domain name (e.g., www.inlanefreight.com) into your browser, it initiates an **HTTP request to the web server** associated with that domain's IP address.
- **Host Header Reveals the Domain**: The browser **includes the domain name in the request's Host header**, which acts as a label to **inform the web server which website is being requested.**
- **Web Server Determines the Virtual Host:** The web server receives the request, **examines the Host header, and consults its virtual host configuration** to find a matching entry for the requested domain name.
- **Serving the Right Content**: Upon identifying the correct virtual host configuration, the web server retrieves the corresponding files and resources associated with that website from its document root and **sends them back to the browser as the HTTP response.**

# Virtual Host Discovery Tools
---
- Specialised `virtual host discovery tools` automate and streamline the process, making it more efficient and comprehensive. 
- ![[Pasted image 20250620071217.png]]
## Gobuster
---
- `gobuster vhost -u http://<target_IP_address> -w <wordlist_file> --append-domain`
	- `The --append-domain flag appends the base domain to each word in the wordlist.`
	- The `-k` flag can ignore SSL/TLS certificate errors.
	- You can use the `-o` flag to save the output to a file for later analysis.