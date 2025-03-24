- Used for sharing files in work and internal environments.
- ```139/tcp   open  netbios-ssn Samba smbd (workgroup: MYGROUP)
  Host script results:
	|_nbstat: NetBIOS name: KIOPTRIX, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
	|_clock-skew: 1h00m04s
	|_smb2-time: Protocol negotiation failed (SMB2)
``` ``` ```
# Metasploit
---
- Start with:
	- `msfconsole`
- `search` -> Search for an exploit / module / enum (auxiliary) script
	- `search smb` / `search auxiliary smb`
- `use auxiliary/scanner/smb/smb_version`
- `show options / info
- `set rhosts 192.168.157.128`
- `run / exploit`
- version: `Samba 2.2.1a`
![[Pasted image 20250324144420.png]]
**SMBCLIENT**
---
- `smbclient -L \\\\<ip>\\` => To start enumerating and listing shares
	- `smbclient -L \\\\192.168.157.128\\`
	- ```Sharename       Type      Comment
        ---------       ----      -------
        IPC$            IPC       IPC Service (Samba Server)
        ADMIN$          IPC       IPC Service (Samba Server)
 - `smbclient \\\\192.168.157.128\\SHARE_NAME
	 - `smbclient \\\\192.168.157.128\\ADMIN$`
	 - `smbclient \\\\192.168.157.128\\IPC$`
	