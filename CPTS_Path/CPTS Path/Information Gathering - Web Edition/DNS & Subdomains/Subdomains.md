- When exploring DNS records, we've primarily focused on the main domain (e.g., example.com) and its associated information.
- However, beneath the surface of this primary domain lies a potential network of subdomains.
- **These subdomains are extensions of the main domain, often created to organise and separate different sections or functionalities of a website.** 
## Why is this important for web reconnaissance?
---
Subdomains often host valuable information and resources that aren't directly linked from the main website. This can include:

- `Development and Staging Environments`: Companies often use subdomains to test new features or updates before deploying them to the main site. Due to relaxed security measures, these environments sometimes contain vulnerabilities or expose sensitive information.
- `Hidden Login Portals`: Subdomains might host administrative panels or other login pages that are not meant to be publicly accessible. Attackers seeking unauthorised access can find these as attractive targets.
- `Legacy Applications`: Older, forgotten web applications might reside on subdomains, potentially containing outdated software with known vulnerabilities.
- `Sensitive Information`: Subdomains can inadvertently expose confidential documents, internal data, or configuration files that could be valuable to attackers.

# Subdomain Enumeration
---
### **1. Active Subdomain Enumeration**
- This involves directly interacting with the target domain's DNS servers to uncover subdomains.
- One method is **==attempting a `DNS zone transfer`==**, where a misconfigured server might inadvertently leak a complete list of subdomains [[DNS]]
- Tools like `dnsenum`, `ffuf`, and `gobuster` can automate this process, using wordlists of common subdomain names or custom-generated lists based on specific patterns. (Bruteforcing)

### **2. Passive Subdomain Enumeration**
- This relies on external sources of information to discover subdomains without directly querying the target's DNS servers.
- ==**One valuable resource is `Certificate Transparency (CT) logs`, public repositories of SSL/TLS certificates.**==
- Another passive approach involves utilising `search engines` like Google or DuckDuckGo. **==By employing specialised search operators (e.g., `site:`)==**, you can filter results to show only subdomains related to the target domain.
