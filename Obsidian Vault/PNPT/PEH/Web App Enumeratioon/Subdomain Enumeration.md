# Good sub domains:
---
- ==**`test`, `stag`, `dev`, `admin` ...==**
# Using assetfinder
---
- **Installation**:
	- `go mod init github.com/tomnomnom/assetfinder`
	- `go install github.com/tomnomnom/assetfinder@latest`
- **Usage:**
	- `assetfinder domain.com` => might be owned by tesla
	- `assetfinder --subs-only domain.com` => only related to domain.com

# Using amass
---
- **[Installation](https://github.com/owasp-amass/amass/blob/master/doc/install.md):**
	- `go install -v github.com/owasp-amass/amass/v4/...@master`
- **Usage:**
	- `amass enum -d domain.com`

# Using httprobe : if the found hosts are alive
---
- **Installation:**
	- `go install github.com/tomnomnom/httprobe@latest`
- **Usage:**
	- `cat file_with_sub_domains.txt | httprobe`

# Using gowitness (screenshots of websites)
---
- `go install github.com/sensepost/gowitness@latest`
- `gowitness single https://domain.com`

# Using subjack
---
- `go get github.com/haccer/subjack`
- `subjack -w subdomains.txt -t 100 -timeout 30 -o results.txt -ssl`
# Automation
---
- Automation script by TCM : https://pastebin.com/MhE6zXVt
- More at: ![[Pasted image 20250402143951.png]]