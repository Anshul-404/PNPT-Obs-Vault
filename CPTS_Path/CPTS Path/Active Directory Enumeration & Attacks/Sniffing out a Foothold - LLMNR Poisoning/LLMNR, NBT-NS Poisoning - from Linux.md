# Link Local Multitask Name Resolution / NBT-NS
---
- Used to identify hosts **when DNS fails to do so**.
- **LLMNR is based upon the Domain Name System (DNS) format** and **allows** ==**hosts on the same local link to perform name resolution**== for other hosts.
- Key flaw : This service **==utilize a user's username and NTLMv2 hash==** when appropriately responded to.
- It uses port `5355` over UDP natively. If LLMNR fails, the NBT-NS will be used. NBT-NS identifies systems on a local network by their NetBIOS name. NBT-NS utilizes port `137` over UDP.
# Theory:
---
- Good time to launch in the morning when people are l**ogging in and trying to access shares / resources on the network**.
- ![[Pasted image 20250806070948.png]]
- Responder Tool:
	- `sudo responder -I tun0 -dwP -v`
	- When it ==senses== this type of traffic, ==it will grab the **hash**==
	- We can **crack the hash** if it is weak
	- ![[Pasted image 20250806071040.png]]
# On Kali (Attacker) - Responder:
---
- We need to run responder as `sudo`.
- `sudo responder -I tun0 -dwv`
	- `I` -> interface
	- `d` -> DHCP (enable answers for DHCP broadcast request)
	- `w` -> Start `WPAD` rogue proxy server. Allows browser to automatically discover our `proxy` without any configuration. 
		- This can be highly effective, especially in large organizations, because it will **capture all HTTP requests by any users that launch Internet Explorer** if the browser has [Auto-detect settings](https://docs.microsoft.com/en-us/internet-explorer/ie11-deploy-guide/auto-detect-settings-for-ie11) enabled.
	- `P` -> ProxyAuth, force authentication for the proxy. (==not needed so can skip if error occurs==)
	- `v` -> Verbose
- Got the NTLMv2`hashes`:
	![[Pasted image 20250806070725.png]]
- ==**NTLMv2 hashes change everytime (though password is always same), NTLMv1 hashes does not change**==. 
- Hashes are also stored in a SQLite database that can be configured in the `Responder.conf` config file, typically located in `/usr/share/responder` unless we clone the Responder repo directly from GitHub.
- **We must run the tool with sudo privileges or as root and make sure the following ports are available** on our attack host for it to function best:
	- `UDP 137, UDP 138, UDP 53, UDP/TCP 389,TCP 1433, UDP 1434, TCP 80, TCP 135, TCP 139, TCP 445, TCP 21, TCP 3141,TCP 25, TCP 110, TCP 587, TCP 3128, Multicast UDP 5355 and 5353`

# Cracking the Caught Hashes
---
- Save the hash in a file like `hashes.txt`
- Crack using **hashcat**:
	- Get help or from `hashcat` modules page to search for hashes
- `hashcat -m 5600 hashes.txt /usr/share/wordlists/rockyou.txt`

# Hashcat Tips:
---
- If already cracked previously then use:
	- `hashcat -m 5600 hashes.txt /usr/share/wordlists/rockyou.txt`
- ![[Pasted image 20250806071402.png]]
- Use `-O` option to optimise
- Better wordlists like **rockyou2021.txt** exists (91 GBs)
- Using `oneruletorulethemall` rule, it mutates password list

# Labs
---
=> Run Responder and obtain a hash for a user account that starts with the letter b. Submit the account name as your answer.
- `sudo responder -I ens224 -dwP -v`
- `backupagent`
- ![[Pasted image 20250806071711.png]]
=>  Crack the hash for the previous account and submit the cleartext password as your answer. 
- `hashcat -m 5600 backupagent.hash /usr/share/wordlists/rockyou.txt`
- ![[Pasted image 20250806072000.png]]
- `h1backup55`
  
=> Run Responder and obtain an NTLMv2 hash for the user wley. Crack the hash using Hashcat and submit the user's password as your answer. 
- ![[Pasted image 20250806072046.png]]
- `hashcat -m 5600 wley.hash /usr/share/wordlists/rockyou.txt`
- ![[Pasted image 20250806072111.png]]
- `transporter@4`