- One of the cornerstones of trust is the **`Secure Sockets Layer/Transport Layer Security` (`SSL/TLS`) protocol**, which encrypts communication between your browser and a website.
- At the heart of SSL/TLS lies the `digital certificate`, a small file that verifies a website's identity and allows for secure, encrypted communication.

# What are Certificate Transparency Logs?
---
- `Certificate Transparency` (`CT`) logs are public, append-only ledgers that record the issuance of SSL/TLS certificates.
- Whenever a Certificate Authority (CA) issues a new certificate, i**t must submit it to multiple CT logs**. Independent organisations maintain these logs and **are open for anyone to inspect.**

# CT Logs and Web Recon
---
![[Pasted image 20250620073125.png]]

## crt.sh lookup
---
`curl -s "https://crt.sh/?q=facebook.com&output=json" | jq -r '.[] | select(.name_value | contains("dev")) | .name_value' | sort -u`