- WHOIS is a widely used query and response protocol designed to **access databases that store information about registered internet resources.** 
- Primarily associated with domain names, WHOIS can also provide details about IP address blocks and autonomous systems. 
- Think of it as a **==giant phonebook for the internet==**, **letting you look up who owns or is responsible for various online assets.**

				`whois inlanefreight.com`

# Information with WHOIS
---
Each WHOIS record typically contains the following information:

- `Domain Name`: The domain name itself (e.g., example.com)
- `Registrar`: The company where the domain was registered (e.g., GoDaddy, Namecheap)
- `Registrant Contact`: The **==person or organization that registered the domain.==**
- `Administrative Contact`: **The person responsible for managing the domain.**
- `Technical Contact`: **The person handling technical issues related to the domain.**
- `Creation and Expiration Dates`: When the domain was registered and when it's set to expire.
- `Name Servers`: **Servers that translate the domain name into an IP address.**
# Why WHOIS?
---
WHOIS data serves as a treasure trove of information for penetration testers during the reconnaissance phase of an assessment. It offers valuable insights into the target organisation's digital footprint and potential vulnerabilities:

- `Identifying Key Personnel`: **WHOIS records often reveal the ==names, email addresses, and phone numbers of individuals responsible for managing the domain==.** This information can be leveraged for social engineering attacks or to identify potential targets for phishing campaigns.
- `Discovering Network Infrastructure`: **Technical details like name servers and IP addresses provide clues about the target's network infrastructure.** This can help penetration testers identify potential entry points or misconfigurations.
- `Historical Data Analysis`: Accessing historical WHOIS records through services like [WhoisFreaks](https://whoisfreaks.com/) can reveal changes in ownership, contact information, or technical details over time. This can be useful for tracking the evolution of the target's digital presence.

# Utilising WHOIS
---
## Scenario 1: Phishing Investigation
---
An email security gateway flags a suspicious email sent to multiple employees within a company. The email claims to be from the company's bank and urges recipients to click on a link to update their account information. A security analyst investigates the email and begins by performing a WHOIS lookup on the domain linked in the email.

The WHOIS record reveals the following:

- `Registration Date`: The domain was registered just a **few days ago.**
- `Registrant`: The registrant's information is hidden behind a **privacy service.**
- `Name Servers`: The name servers are associated with a known **bulletproof hosting provider often used for malicious activities.**

## Scenario 2: Malware Analysis
---
A security researcher is analysing a new strain of malware that has infected several systems within a network. The malware communicates with a remote server to receive commands and exfiltrate stolen data. To gain insights into the threat actor's infrastructure, the researcher performs a WHOIS lookup on the domain associated with the command-and-control (C2) server.

The WHOIS record reveals:

- `Registrant`: The domain is registered to an individual u**sing a free email service known for anonymity.**
- `Location`: The registrant's address is in a country with a high **prevalence of cybercrime.**
- `Registrar`: The domain was registered through a registrar with a **history of lax abuse policies.**

## Scenario 3: Threat Intelligence Report
---
Analysts gather WHOIS data on multiple domains associated with the group's past campaigns to compile a comprehensive threat intelligence report.

By analysing the WHOIS records, analysts uncover the following patterns:

- `Registration Dates`: The domains were registered in clusters, often shortly before major attacks.
- `Registrants`: The registrants use various aliases and fake identities.
- `Name Servers`: The domains often share the same name servers, suggesting a common infrastructure.
- `Takedown History`: Many domains have been taken down after attacks, indicating previous law enforcement or security interventions.