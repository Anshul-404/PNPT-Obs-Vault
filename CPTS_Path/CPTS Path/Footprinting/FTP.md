-  In an FTP connection, two channels are opened. First, the ==**client and server establish a control channel through TCP port 21.**== 
- The client sends commands to the server, and the server returns status codes. 
- Then both **==communication participants can establish the data channel via TCP port 20.==** This channel is used exclusively for **data transmission**, and the protocol watches for errors during this process. 
- FTP is a `clear-text` protocol that can sometimes be sniffed if conditions on the network are right. However, there is also the possibility that a server offers `anonymous FTP`.
# Active vs Passive modes
---
 - In the ==**active** variant,== ==**the client establishes the connection as described via TCP port 21**== and thus ==**informs the server via which client-side port the server can transmit**== its responses. 
 - However, **if a firewall protects the client,** the server cannot reply because all **external connections are blocked.** 
 - For this purpose, the ==**passive mode**== has been developed. Here, the ==**server announces a port** through which the **client can establish the data channel.**== Since the client initiates the connection in this method, the firewall does not block the transfer.

# TFTP
---
- Trivial File Transfer Protocol (TFTP) is simpler than FTP and performs file transfers between client and server processes. 
- However, it **==does not provide user authentication==** and other valuable features supported by FTP.
- In addition, while FTP uses TCP, **==TFTP uses UDP==**, making it an unreliable protocol and causing it to use UDP-assisted application layer recovery.

# Files
---
- Config File: `cat /etc/vsftpd.conf | grep -v "#"`
- FTPUSERS: `cat /etc/ftpusers` -> This file is used to **deny certain users** access to the FTP service.

# Dangerous Settings
---
![[Pasted image 20250615072603.png]]

# Common Stuff
---
- vsFTPd Detailed Output--> `debug` command
- ![[Pasted image 20250615072735.png]]
- ![[Pasted image 20250615072810.png]]

# Footprinting the Service
---
- `nmap -sV -p21 -sC -A 10.129.14.136`