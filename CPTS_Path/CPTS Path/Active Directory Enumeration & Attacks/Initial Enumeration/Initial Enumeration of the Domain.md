![[Pasted image 20250806061337.png]]
![[Pasted image 20250806061444.png]]![[Pasted image 20250806061457.png]]
![[Pasted image 20250806061532.png]]

# TTPs
---
- We need to set a game plan for ourselves and tackle it piece by piece. Everyone works in slightly different ways, so as we gain more experience, we'll start to develop our own repeatable methodology that works best for us.
- We will start with **`passive` identification of any hosts in the network, followed by `active` validation** of the results to find out more about each host (what **services are running, names, potential vulnerabilities,** etc.)
- Once we know what hosts exist, **we can proceed with probing those hosts**, looking for any interesting data we can glean from them. 
- After we have accomplished these tasks, we should stop and regroup and look at what info we have. At this time, we'll **hopefully have a set of credentials or a user account to target** for a foothold onto a domain-joined host or have the ability to begin credentialed enumeration from our Linux attack host.
# Identifying Hosts
---
- We can use **`Wireshark` and `TCPDump` to "put our ear to the wire"** and see what hosts and types of **==network traffic we can capture.==**
## Start Wireshark on ea-attack01
- `$sudo -E wireshark`
- ARP packets ==**make us aware of the hosts: 172.16.5.5, 172.16.5.25 172.16.5.50, 172.16.5.100, and 172.16.5.125.**==
	- ![[Pasted image 20250806061926.png]]
- MDNS makes us **aware of the ACADEMY-EA-WEB01 host.**
	- ![[Pasted image 20250806061843.png]]
- If we are on a host without a GUI (which is typical), **we can use [tcpdump](https://linux.die.net/man/8/tcpdump), [net-creds](https://github.com/DanMcInerney/net-creds), and [NetMiner](https://www.netminer.com/en/product/netminer.php), etc., to perform the same function**

## Tcpdump Output
- `sudo tcpdump -i ens224 `

- Depending on the host you are on, **you may already have a network ==monitoring tool built-in, such as `pktmon.exe==`**, which was added to all editions of Windows 10.
- As a note for testing, it's always a good idea to **==save the PCAP traffic==** you capture. **You can review it again later to look for more hints,** and it makes for great additional information to include while writing your reports.

- Our first look at network traffic pointed us to a couple of hosts via `MDNS` and `ARP`. Now let's utilize a tool called **`Responder` to analyze network** traffic and determine if anything else in the domain pops up.

## Starting Responder in Analyze mode
---
- `sudo responder -I ens224 -A `
- Notice below that we found a **few unique hosts not previously mentioned in our Wireshark captures**
- It's worth noting these down as **==we are starting to build a nice target list of IPs and DNS hostnames.==**

# FPing
---
- Now let's perform some **active checks starting with a quick ICMP sweep of the subnet** using `fping`.
- [Fping](https://fping.org/) provides us with a similar capability as the standard ping application in that it utilizes ICMP requests and replies to reach out and interact with a host.
- Where **fping shines is in its ability to issue ICMP packets against a list of multiple hosts at once and its scriptability.**
- Also, it works in a round-robin fashion, querying hosts in a cyclical manner instead of waiting for multiple requests.

- Here we'll start `fping` with a few flags: 
	- `a` to show targets that are alive
	- `s` to print stats at the end of the scan
	- `g` to generate a target list from the CIDR network
	- and `q` to not show per-target results.
	  
- **`fping -asgq 172.16.5.0/23`**

# Nmap Scanning
---
- We are looking to determine **what services each host is running,** identify **critical hosts such as `Domain Controllers` and `web servers`,** and identify **==potentially vulnerable hosts==** to probe later.
	- **`sudo nmap -v -A -iL hosts.txt -oN /home/htb-student/Documents/host-enum`**
	- The [-A (Aggressive scan options)](https://nmap.org/book/man-misc-options.html) scan will perform several functions.

- We can see from the output above that we have a potential host running **an outdated operating system ( Windows 7, 8, or Server 2008 based on the output).** 
- This is of interest to us since it means there are l**egacy operating systems running in this AD environment**. 
- It also means there is potential for older exploits like **==EternalBlue, MS08-067==**, and others to work and provide us with a SYSTEM level shell.

# Identifying Users
---
- If our client does not provide us with a user to start testing with (which is often the case), we will need to find a way to **establish a foothold** in the domain by either **==obtaining clear text credentials or an NTLM password hash for a user==**, a **SYSTEM shell on a domain-joined host, or a shell in the context of a domain user account.**

## Kerbrute - Internal AD Username Enumeration
- [Kerbrute](https://github.com/ropnop/kerbrute) can be a **stealthier option for domain account enumeration**. 
- It takes advantage of the fact that **Kerberos pre-authentication** failures often will not trigger logs or alerts.
- We will use Kerbrute in conjunction with the `jsmith.txt` or `jsmith2.txt` user lists from [Insidetrust](https://github.com/insidetrust/statistically-likely-usernames)
- This repository **==contains many different user lists that can be extremely useful==** when attempting to enumerate users when starting from an unauthenticated perspective.

- Enumerating Users with Kerbrute:
	- `kerbrute userenum -d INLANEFREIGHT.LOCAL --dc 172.16.5.5 jsmith.txt -o valid_ad_users`

# Identifying Potential Vulnerabilities
---
- It is very common for **third-party services to run in the context of `NT AUTHORITY\SYSTEM ` account** by default.
- A `SYSTEM` account on a `domain-joined` host will be ==**able to enumerate Active Directory by impersonating the computer account**==, which is essentially just another kind of user account.
- Having **==SYSTEM-level access within a domain environment is nearly equivalent to having a domain user account.==**
- ![[Pasted image 20250806063052.png]]

# Labs
---
=> From your scans, what is the "commonName" of host 172.16.5.5 ?
- `nmap 172.16.5.5  -A`
- `ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL`
  
=> What host is running "Microsoft SQL Server 2019 15.00.2000.00"? (IP address, not Resolved name) 
- `nmap 172.16.5.0/24 -p139,445 -sC `
- ![[Pasted image 20250806064332.png]]