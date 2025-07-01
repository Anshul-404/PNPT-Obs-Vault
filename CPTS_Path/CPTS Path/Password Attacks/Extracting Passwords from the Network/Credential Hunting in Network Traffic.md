- Legacy systems, misconfigured services, or test applications launched **without HTTPS can still result in the use of unencrypted protocols such as HTTP or SNMP.**
- These gaps present a valuable opportunity for attackers: **==`the chance to hunt for credentials in cleartext network traffic`.==**
- The table below lists several common protocols alongside their encrypted counterparts:
	- ![[Pasted image 20250701071523.png]]

# Wireshark
---
- [Wireshark](https://www.wireshark.org/) is well known **packet analyzer t**hat comes pre-installed nearly all penetration testing Linux distributions.
- It features a ==**powerful [filter engine](https://www.wireshark.org/docs/man-pages/wireshark-filter.html) that allows for efficient searching**== through both live and captured network traffic.
- Some basic but useful filters include:
	- ![[Pasted image 20250701071657.png]]
- In Wireshark, it's possible to locate packets that contain specific bytes or strings.
	- One way to do this is by using a **==display filter such as `http contains "passw"`==**
	- Alternatively, you can navigate **==to `Edit > Find Packet` and enter the desired search query manually==**. For example, you might search for packets containing the string `"passw"`:
	- ![[Pasted image 20250701071744.png]]
- **==Following TCP streams is also useful to read full conversations.==**

# Pcredz
---
- [Pcredz](https://github.com/lgandx/PCredz) is a tool that can be used to ==**extract credentials from live traffic== or network packet captures.** Specifically, it supports extracting the following information:
	- Credit card numbers
	- POP credentials
	- SMTP credentials
	- IMAP credentials
	- SNMP community strings
	- FTP credentials
	- Credentials from HTTP NTLM/Basic headers, as well as HTTP Forms
	- NTLMv1/v2 hashes from various traffic including DCE-RPC, SMBv1/2, LDAP, MSSQL, and HTTP
	- Kerberos (AS-REQ Pre-Auth etype 23) hashes
- The following command can be used to **run `Pcredz` against a packet capture file:**
	- `./Pcredz -f demo.pcapng -t -v`