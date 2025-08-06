[Netsh](https://docs.microsoft.com/en-us/windows-server/networking/technologies/netsh/netsh-contexts) is a **Windows command-line tool that can help with the network configuration of a particular Windows system**. Here are just some of the networking related tasks we can use `Netsh` for:

- `Finding routes`
- `Viewing the firewall configuration`
- `Adding proxies`
- `Creating port forwarding rules`

![[Pasted image 20250729072328.png]]

- Let's take an example of the below scenario **where our compromised host is a Windows 10-based IT admin's workstation (`10.129.15.150`, `172.16.5.25`).**
- We can use **`netsh.exe` to forward all data received on a specific port (say 8080) to a remote host on a remote port.** This can be performed using the below command.

# Using Netsh.exe to Port Forward
---
- `netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=10.129.15.150 connectport=3389 connectaddress=172.16.5.25`
	- `10.129.15.150` -> Pivot (compromised) Host
	- `172.16.5.25` -> Internal machine to forward traffic on

## Verifying Port Forward
- `netsh.exe interface portproxy show v4tov4`

- After configuring the `portproxy` on our Windows-based pivot host, **we will try to connect to the 8080 port of this host from our attack host using xfreerdp**:
	- `xfreerdp3 /v:10.129.15.150:8080 /u:'victor' /p:'pass@123'`
