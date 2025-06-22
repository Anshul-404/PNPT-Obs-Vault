 `94.237.51.163` `45390`
 vHosts needed for these questions:
- `inlanefreight.htb`

=> What is the IANA ID of the registrar of the inlanefreight.com domain? 
- ![[Pasted image 20250621070130.png]]

=> What http server software is powering the inlanefreight.htb site on the target system? Respond with the name of the software, not the version, e.g., Apache. 
![[Pasted image 20250621070249.png]]

=> What is the API key in the hidden admin directory that you have discovered on the target system?
- Use larger wordlist for VHOST bruteforcing:
	- `gobuster vhost -u http://inlanefreight.htb:54372/ -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt  --append-domain -t 100`
	- ![[Pasted image 20250622054935.png]]
	- Even more (subdomain of subdomain):
		- ![[Pasted image 20250622055135.png]]
	- Scraping both the subdomains:
		- `python3 ReconSpider.py http://dev.web1337.inlanefreight.htb:54372`
		- ![[Pasted image 20250622055758.png]]
		- ![[Pasted image 20250622060555.png]]
	- ![[Pasted image 20250622060647.png]]