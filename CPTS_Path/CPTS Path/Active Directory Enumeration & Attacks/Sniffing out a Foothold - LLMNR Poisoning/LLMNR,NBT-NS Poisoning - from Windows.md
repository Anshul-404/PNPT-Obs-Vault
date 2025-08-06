- **LLMNR & NBT-NS poisoning is possible from a Windows host as wel**l. In the last section, we utilized Responder to capture hashes. 
- This section will **explore the tool [Inveigh](https://github.com/Kevin-Robertson/Inveigh) and attempt to capture** another set of credentials.
# Inveigh - Overview
---
- The tool [Inveigh](https://github.com/Kevin-Robertson/Inveigh) **works similar to Responder, but is written in PowerShell and C#**. 
- Inveigh **can listen to IPv4 and IPv6 and several other protocols, including `LLMNR`, DNS, `mDNS`, NBNS, `DHCPv6`, ICMPv6, `HTTP`, HTTPS, `SMB`, LDAP, `WebDAV`, and Proxy Auth**. 
- The tool is available in the `C:\Tools` directory on the provided Windows attack host.
# Using Inveigh
---
- `Import-Module .\Inveigh.ps1`
- `(Get-Command Invoke-Inveigh).Parameters`
	- ![[Pasted image 20250806072422.png]]
	  
- Let's start Inveigh **with ==LLMNR and NBNS spoofing==, and output to the console and write to a file**:
	- `Invoke-Inveigh Y -NBNS Y -ConsoleOutput Y -FileOutput Y`

# C# Inveigh (InveighZero)
---
- The **PowerShell version of Inveigh is the original version and is no longer updated.** 
- The tool author **==maintains the C# version, which combines the original PoC C# code and a C# port==** of most of the code from the PowerShell version.
- Copy of both the PowerShell and compiled executable version of the tool in the `C:\Tools` folder on the target host in the lab, **==but it is worth walking through the exercise (and best practice) of compiling it yourself using Visual Studio.==**


- Let's go ahead and run the C# version with the defaults and start capturing hashes:
	- `.\Inveigh.exe`
	- ![[Pasted image 20250806072641.png]]
- As we can see, **the tool starts and shows which options are enabled by default and which are not**. 
- The options with a `**[+]` are default and enabled by default** and the **ones with a `[ ]` before them are disabled.**
- We can hit the `esc` key to enter the console while Inveigh is running.
- After **typing `HELP` and hitting enter, we are presented with several options:**
	- ![[Pasted image 20250806072706.png]]
- We can quickly ==**view unique captured hashes by typing `GET NTLMV2UNIQUE`.**==
	- ![[Pasted image 20250806072729.png]]
- We can type in **==`GET NTLMV2USERNAMES` and see which usernames==** we have collected.
- This is helpful if we want a listing of users to **perform additional enumeration against and see which are worth attempting to crack offline** using Hashcat.

# Detection
---
- It is not always possible to disable LLMNR and NetBIOS, and therefore we need ways to detect this type of attack behavior. 
- One way is to use the attack against the **attackers by injecting LLMNR and NBT-NS requests for non-existent hosts across different subnets and alerting if any of the responses receive answers** which would be indicative of an attacker spoofing name resolution responses. This [blog post](https://www.praetorian.com/blog/a-simple-and-effective-way-to-detect-broadcast-name-resolution-poisoning-bnrp/) explains this method more in-depth.
- Furthermore, hosts can be monitored for traffic on ports UDP 5355 and 137, and event IDs [4697](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4697) and [7045](https://www.manageengine.com/products/active-directory-audit/kb/system-events/event-id-7045.html) can be monitored for. Finally, we can monitor the registry key `HKLM\Software\Policies\Microsoft\Windows NT\DNSClient` for changes to the `EnableMulticast` DWORD value. A value of `0` would mean that LLMNR is disabled.

# Labs
---
=> Run Inveigh and capture the NTLMv2 hash for the svc_qualys account. Crack and submit the cleartext password as the answer. 
- `.\inveigh.exe`
	![[Pasted image 20250806073545.png]]
- `hashcat -m 5600 svc_qualsys.hash  /usr/share/wordlists/rockyou.txt`
- ![[Pasted image 20250806073757.png]]
- `security#1`