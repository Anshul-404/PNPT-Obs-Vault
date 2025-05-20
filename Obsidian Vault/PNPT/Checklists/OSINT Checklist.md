Note:- This **==checklist only covers some of the parts that would probably be required in the exam==** 
Also covered in: [[External Pentest Checklist]]
#  Search Engine Operators
---
[[Search Engine OSINT]]
Google Fu - [[Google Fu]]
- Wildcards: `"heath adams" the * mentor` => missing *
	- **Hunting Passwords**
		- `site:tesla.com password filetype:pdf`
		- `site:tesla.com password filetype:xlsx`
		- `"tesla.com" filetype:xlsx password`
	- **Subdomains**
		- `site:tesla.com -www`take out / without www)
	- **In title/url/text**
		-  `"heath adams" intext:password`=> Commonly used, must show up in text in website
		- `"heath adams" inurl:password`
		- `"heath adams" intitle:password` => Must show up in title of website	
	- https://www.google.com/advanced_search

# Exif Data
----
[[EXIF Data]]
- `jimpl.com`, `exifdata.com`, `exiftools.com`
- `exiftool some_file.ext`
- `file some_file.ext`

# Discovering Email Addresses
---
[[Discovering Email Addresses]]
- **Start with google Search** **for job roles on the company**
- Find email patterns using:
	- `www.hunter.io`
	- **==Common ones are `flast@domain.com`, `first.last@domain.com`, `f.last@domain.com`==**
- `connect.clearbit.com`- Chrome Extension
- To Verify : `www.accounts.google.com`, `www.emailhippo.com`

# Password OSINT
---
[[Hunting Breached Credentials]]
[[Password OSINT]]
- Find repeated offenders
- Paste in the docx file : "Breached Accounts"
	1. **Find as much data as you can**
	2. Try ==**credential stuffing, password spraying**==
		1. `Winter2021`, `Winter21`, `Winter21!`
- ==**Find leaked passwords for emails**==
	-   www.scylla.sh, www.dehashed.com, www.haveibeenpwned.com
- Find **==more information about the found users==** in other companies (more **email addresses, more passwords related to the account**)

# ==**Hunting Username and Accounts==**
---
[[Username and Account OSINT]]
[[Username OSINT]]

# Searching for People
----
[[Searching for People]]

# ==Discovering Birth Dates==
----
[[Discovering Birthdates]]
- `"heath adams" intext:"happy birthday"`
- `"heath adams" intext:"happy birthday" site:twitter.com`
- `"heath adams" intext:"happy birthday" site:facebook.com`

# Instagram OSINT
---
[[Instagram OSINT]]

# ==**Linkedin OSINT==**
----
[[Linkedin OSINT]]
- **Reverse image search the profile image**- Different username
- ==**Connections**==
- **==Contact Information**
- **==Activity==**
- **Education**
- **Endorsements and recommendations**
- **Work History**

# ==Website OSINT== 
---
[[Website OSINT Part - 1]]
[[Website OSINT Part - 2 + Subdomains]]
[[Website OSINT Part - 3 Tools]]

- BuiltWith - [https://builtwith.com/](https://builtwith.com/)
- Domain Dossier - [https://centralops.net/co/](https://centralops.net/co/)
- DNSlytics - [https://dnslytics.com/reverse-ip](https://dnslytics.com/reverse-ip)
- SpyOnWeb - [https://spyonweb.com/](https://spyonweb.com/)
- Virus Total - [https://www.virustotal.com/](https://www.virustotal.com/)
- Visual Ping - [https://visualping.io/](https://visualping.io/)
- Back Link Watch - [http://backlinkwatch.com/index.php](http://backlinkwatch.com/index.php)
- View DNS - [https://viewdns.info/](https://viewdns.info/)

#  ==Hunting Business Information== 
----
[[Hunting Businesses]]
- ==**Identify Employees**==
	- **Head Honchos (Executives)**
	- **Organizational Structure**
	- **Social Media posts by Employees**
		- **Desk pictures, ID cards, Badges, Dress Code**
	- **Job Description (What the company is working on / technology)
	- [[Discovering Email Addresses]]
- **==Linkedin==**:
	- **Company About**: Headquarter Address, website, phone number
	- People: we can **image OSINT**
	- **Videos**, Images and posts
	- People Also viewed and Recommendations
- ==GoogleFu==:
	- Search for **specific designations** and **job roles** at the company
	- Search for just **`"at TCM Security" site: linkedin.com`**
	- Searching for **jobs** `"company name" site:indeed.com`
- Websites:
	- Open Corporates - [https://opencorporates.com/](https://opencorporates.com/)
		- Addresses, CEOs, Reports, Registration Date
	- AI HIT - [https://www.aihitdata.com/](https://www.aihitdata.com/) 
	- Indeed: Job Details, Technologies or software,  
[[Social Media OSINT]]


[[Subdomain Enumeration]]