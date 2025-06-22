- Domain information is a core component of any penetration test, and it is not just about the **subdomains but about the ==entire presence on the Internet.**== 
- Therefore, we gather information and try to understand the ==**company's functionality and which technologies and structures are necessary for services**== to be offered successfully and efficiently.
- Scrutinize the company's main website first and then 3rd party OSINT tools.

# Online Presence
---
- Once we have a basic understanding of the company and its services, we can get a first impression of its presence on the Internet.
- The first point of presence on the Internet may be the `SSL certificate` from the company's main website that we can examine.
- Another source to find more **==subdomains is [crt.sh](https://crt.sh/).==**
	- `curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq .`
	- If needed, we can also have them filtered by the unique subdomains.
	- `curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\n/,"\n");}1;' | sort -u`

- Next, we can **identify the hosts directly accessible from the Internet** and not hosted by third-party providers. 
	- `for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done`
- Once we see which hosts can be investigated further, we can generate a list of IP addresses with a minor adjustment to the `cut` command and run them through `Shodan`.

- **[Shodan](https://www.shodan.io/) can be used to find devices and systems permanently connected to the Internet** like `Internet of Things` (`IoT`). It searches the Internet for open TCP/IP ports and filters the system
	- `for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f4 >> ip-addresses.txt;done`
	- `for i in $(cat ip-addresses.txt);do shodan host $i;done`

- DNS Records:
	- `dig any inlanefreight.com`

- `A` records: We recognize the IP addresses that point to a specific (sub)domain through the A record. Here we only see one that we already know.
- `MX` records: The mail server records show us which mail server is responsible for managing the emails for the company. Since this is handled by google in our case, we should note this and skip it for now.
- `NS` records: These kinds of records show which name servers are used to resolve the FQDN to IP addresses. Most hosting providers use their own name servers, making it easier to identify the hosting provider.
- `TXT` records: this type of record often contains verification keys for different third-party providers and other security aspects of DNS, such as [SPF](https://datatracker.ietf.org/doc/html/rfc7208), [DMARC](https://datatracker.ietf.org/doc/html/rfc7489), and [DKIM](https://datatracker.ietf.org/doc/html/rfc6376), which are responsible for verifying and confirming the origin of the emails sent. Here we can already see some valuable information if we look closer at the results.