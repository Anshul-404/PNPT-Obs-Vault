- The `Simple Mail Transfer Protocol` (`SMTP`) is a protocol for sending emails in an IP network. It can be used between an email client and an outgoing mail server or between two SMTP servers.
- SMTP is often combined with the IMAP or POP3 protocols, which can fetch emails and send emails.
- By default, **SMTP servers accept connection requests on port `25`**. However, **newer SMTP servers also use other ports such as TCP port `587`.**
- SMTP works unencrypted without further measures and transmits all commands, data, or authentication information in plain text.
- Under certain circumstances, a server uses a port other than the standard TCP port `25` for the encrypted connection, for example, TCP port `465`.

# Default Configuration
---
- `/etc/postfix/main.cf`

# Commands
---
![[Pasted image 20250616050827.png]]
- The **==command `VRFY` can be used to enumerate existing users on the system.==** 
- However, this does not always work. Depending on how the SMTP server is configured, the SMTP **==server may issue `code 252` and confirm the existence of a user that does not exist on the system.==**
- A list of all SMTP response codes can be found [here](https://serversmtp.com/smtp-error/).
	- `VRFY root`

# Web Proxy
---
- Sometimes we may have to work through a web proxy. 
- We can also **make this web proxy connect to the SMTP server**. The command that we would send would then look something like this: 
	- `CONNECT 10.129.14.128:25 HTTP/1.0`

# Sending Mail
---
![[Pasted image 20250616051050.png]]

# Dangerous Settings
---
- To **prevent** the sent emails from being **filtered by spam filters** and not reaching the recipient, the ==**sender can use a relay server that the recipient trusts.**== 
- It is an **SMTP server that is known and verified by all others**. As a rule, **==the sender must authenticate himself to the relay server before using it.==**
- Often, administrators have no overview of which IP ranges they have to allow.
- ==**They allow all IP addresses not to cause errors in the email traffic**== and thus not to disturb or unintentionally interrupt the communication with potential and current customers.

	- `mynetworks = 0.0.0.0/0`
	- With this setting, **this SMTP server can send fake emails and thus initialize communication between multiple parties.** Another attack possibility would be to spoof the email and read it.

# Footprinting
---
- `nmap 10.129.14.128 -sC -sV -p25`
- `nmap 10.129.14.128 -p25 --script smtp-open-relay -v`