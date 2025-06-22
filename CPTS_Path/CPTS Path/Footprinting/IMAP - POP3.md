- With the help of the `Internet Message Access Protocol` (`IMAP`), access to emails from a mail server is possible.
- Unlike the `Post Office Protocol` (`POP3`), IMAP allows online management of emails directly on the server and supports folder structures.
- Thus, it is a network protocol for the online management of emails on a remote server.
- The client establishes the connection to the server via port `143`. For communication, it uses text-based commands in `ASCII` format.
- Access to the desired mailbox is only possible after successful authentication.
- Depending on the method and implementation used, the encrypted connection uses the standard port `143` or an alternative port such as `993`.

# Default Configuration
---
**IMAP commands**
![[Pasted image 20250616062050.png]]
**POP3 Commands**
![[Pasted image 20250616062105.png]]

# Dangerous Settings
---
- Nevertheless, configuration options that were improperly configured could allow us to obtain more information, such as debugging the executed commands **==on the service or logging in as anonymous==**
- ![[Pasted image 20250616062152.png]]

# Footprinting
---
- `nmap 10.129.14.128 -sV -p110,143,993,995 -sC`
	- cURL: `curl -k 'imaps://10.129.14.128' --user user:p4ssw0rd`
- OpenSSL - TLS Encrypted Interaction POP3 `openssl s_client -connect 10.129.14.128:pop3s`
- OpenSSL - TLS Encrypted Interaction IMAP: `openssl s_client -connect 10.129.14.128:imaps`
- If we get `4047C51E777F0000:error:0A00010A:SSL routines:can_renegotiate:wrong ssl version:../ssl/ssl_lib.c:2833`
  **==error, use `-nocommands` option with OpenSSL:==**
	-  `openssl s_client -connect 10.129.202.20:pop3s -nocommands`

# Lab
---
=> Figure out the exact organization name from the IMAP/POP3 service and submit it as the answer.
- `nmap 10.129.141.193 -sV -p110,143,993,995 -sC`

=> What is the FQDN that the IMAP and POP3 servers are assigned to?
- `nmap 10.129.141.193 -sV -p110,143,993,995 -sC`

=> `Enumerate the IMAP service and submit the flag as the answer. (Format: HTB{...}) `
- `openssl s_client -connect 10.129.75.141:imaps`

=>  What is the customized version of the POP3 server? 
- ` nc 10.129.75.141 110`

=> What is the admin email address? Try to access the emails on the IMAP server and submit the flag as the answer.
- Connect with robin's credentials and list:
	- `openssl s_client -connect 10.129.75.141:imaps`
	- `A1 LOGIN robin robin`
	- `A2 LIST "" *`
	- ![[Pasted image 20250616070258.png]]

- Select DEV..DEPARTMENT.INT mailbox
	- `A3 SELECT DEV.DEPARTMENT.INT`
	- ![[Pasted image 20250616070431.png]]
- Read everything full email:
	- `FETCH 1 BODY[HEADER.FIELDS (FROM TO SUBJECT DATE)]`
	- ![[Pasted image 20250616070450.png]]