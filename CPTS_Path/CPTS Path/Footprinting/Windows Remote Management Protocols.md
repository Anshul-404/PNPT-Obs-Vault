The main components used for remote management of Windows and Windows servers are the following:
- Remote Desktop Protocol (RDP)
- Windows Remote Management (WinRM)
- Windows Management Instrumentation (WMI)

# RDP
---
- The Remote Desktop Protocol (RDP) is a protocol developed by Microsoft for remote access to a computer running the Windows operating system.
-  Typically utilizing **TCP port 3389** as the transport protocol. 
- For an RDP session to be established, **both the network firewall and the firewall on the server must allow connections from the outside.**
- The `Remote Desktop` **service is installed by default on Windows servers** and does not require additional external applications. 
- This service can be **activated using the `Server Manager` and comes with the default setting to allow connections** to the service only to hosts with [Network level authentication](https://en.wikipedia.org/wiki/Network_Level_Authentication) (`NLA`).
-  If you're attacking or testing an RDP server with **NLA enabled**, you **can’t see the login screen** until you provide valid credentials.

- **Footprinting**:
---
- We can determine if NLA is enabled on the server or not:
	- `nmap -sV -sC 10.129.201.248 -p3389 --script rdp*`
- In addition, **we can use `--packet-trace` to track the individual packages** and inspect their contents manually. 
- **==We can see that the `RDP cookies`==** (`mstshash=nmap`) used by Nmap to interact with the RDP server can be identified by `threat hunters` and various security services such as [Endpoint Detection and Response](https://en.wikipedia.org/wiki/Endpoint_detection_and_response) (`EDR`).

- **RDP Security Check**
---
- `sudo cpan` -> Install first
- `git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git && cd rdp-sec-check`
- `./rdp-sec-check.pl 10.129.201.248`
	- A Perl script named [rdp-sec-check.pl](https://github.com/CiscoCXSecurity/rdp-sec-check) has also been developed by [Cisco CX Security Labs](https://github.com/CiscoCXSecurity) that can **unauthentically identify the security settings of RDP servers based on the handshakes.**

# WinRM
---
- The Windows Remote Management (`WinRM`) is a simple Windows **integrated remote management protocol** based on the command line.
- WinRM uses the Simple Object Access Protocol (`SOAP`) to establish connections to remote hosts and their applications.
- WinRM **must be explicitly enabled and configured starting with Windows 10**
- `TCP` ports `5985` and `5986` for communication, with the last port `5986 using HTTPS`
- Another component that fits WinRM for administration is **Windows Remote Shell (`WinRS`)**, which lets us ==execute **arbitrary commands on the remote system.**==

## Footprinting
---
- ` nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n`
- If we want to **find out** whether one or more r**emote servers can be reached via WinRM, we can easily do this with the help of PowerShell.**:
- The [Test-WsMan](https://docs.microsoft.com/en-us/powershell/module/microsoft.wsman.management/test-wsman?view=powershell-7.2) cmdlet is responsible for this, and the host's name in question is passed to it. . In Linux-based environments, **==we can use the tool called [evil-winrm](https://github.com/Hackplayers/evil-winrm),==**
	- ` evil-winrm -i 10.129.201.248 -u Cry0l1t3 -p P455w0rD!`

# WMI
---
- Windows Management Instrumentation (WMI) is Microsoft's implementation and also an extension of the Common Information Model (CIM), core functionality of the standardized Web-Based Enterprise Management (WBEM) for the Windows platform.
- WMI allows read and write access to almost all settings on Windows systems.
- WMI is typically **accessed via PowerShell, VBScript, or the Windows** Management Instrumentation Console (`WMIC`). 

## Footprinting - impacket-WMIexec
---
- `impacket-wmiexec Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname"`
