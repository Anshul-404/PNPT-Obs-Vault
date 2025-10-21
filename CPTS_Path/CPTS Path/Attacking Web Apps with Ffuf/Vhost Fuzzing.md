## Vhosts vs. Sub-domains
---
- The key difference between VHosts and sub-domains is that a **==VHost is basically a 'sub-domain' served on the same server and has the same IP==**, such that a **single IP could be serving two or more different websites.**
	- `VHosts may or may not have public DNS records.`
- In many cases, **many websites would actually have sub-domains that are not public** and will not publish them in public DNS records, and hence if we visit them in a browser, we would fail to connect, as the public DNS would not know their IP.
- This is where **we utilize `VHosts Fuzzing` on an IP we already have.** We will run a scan and test for scans on the same IP, and then we will be able to identify both public and non-public sub-domains and VHosts.

# Vhosts Fuzzing
---
- To scan for VHosts, without manually adding the entire wordlist to our `/etc/hosts`, we will be fuzzing HTTP headers, **specifically the `Host:` header.** :
	- `ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb'`
- We see that all words in the wordlist are returning `200 OK`! This is expected, as we are simply changing the header while visiting `http://academy.htb:PORT/`. So, we know that we will always get `200 OK`. However, if the VHost does exist and we send a correct one in the header, we should get a different response size, as in that case, we would be getting the page from that VHosts, which is likely to show a different page.