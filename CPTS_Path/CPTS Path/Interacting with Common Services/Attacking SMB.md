- Server Message Block (SMB) is a communication protocol created for providing **shared access to files and printers across nodes on a network.** 
- Initially, it was designed to run on top of **NetBIOS over TCP/IP (NBT) using TCP port `139` and UDP ports `137` and `138`.**
- Samba is a Unix/Linux-based open-source implementation of the SMB protocol. It also allows Linux/Unix servers and Windows clients to use the same SMB services.
- Another protocol that is commonly related to SMB is **[MSRPC (Microsoft Remote Procedure Call)](https://en.wikipedia.org/wiki/Microsoft_RPC).**
- RPC **provides an application developer a generic way to execute a procedure (a.k.a. a function) in a local or remote process without having to understand the network protocols** used to support the communication, as specified in [MS-RPCE](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rpce/290c38b1-92fe-4229-91e6-4fc376610c15), which defines an RPC over SMB Protocol that can use SMB Protocol named pipes as its underlying transport.

# Enumeration
---
- [[SMB]]
- `sudo nmap 10.129.14.128 -sV -sC -p139,445`
- The Nmap scan reveals essential information about the target:
	- SMB version (Samba smbd 4.6.2)
	- **Hostname HTB**
	- **Operating System is Linux based on SMB implementation**

# Misconfigurations
---
- SMB can be **==configured not to require authentication, which is often called a `null session`==**. Instead, we can log in to a system with no username or password.

## Anonymous Authentication
- Most tools that interact with SMB allow null session connectivity, including `smbclient`, `smbmap`, `rpcclient`, or `enum4linux`. 

- **File Share**:
	- Using the **option ==-N, we tell smbclient to use the null session:**==
		- `smbclient -N -L //10.129.14.128`
- **Smbmap**:
	- `smbmap -H 10.129.14.128`
	- Using `smbmap` **with the `-r` or `-R` (recursive) option,** one can browse the directories:
	- `smbmap -H 10.129.14.128 -r notes`
		- ![[Pasted image 20250708065234.png]]
	- Upload / Download:
		- `smbmap -H 10.129.14.128 --download "notes\note.txt"`
		- `smbmap -H 10.129.14.128 --upload test.txt "notes\test.txt"`

- **Remote Procedure Call (RPC)**:
	- [[SMB]]
	- We can use the `rpcclient` tool with a null session to enumerate a workstation or Domain Controller.
	- The `rpcclient` tool offers us **many different commands to execute specific functions on the SMB server to gather information** or modify server attributes like a username:
		- `rpcclient -U'%' 10.10.110.17`
		- `enumdomusers`
		- ![[Pasted image 20250615080304.png]]

- **Enum4linux**:
	- `./enum4linux-ng.py 10.10.11.45 -A -C`


# Protocol Specifics Attacks
---
- If a null session is not enabled, we will need credentials to interact with the SMB protocol. Two common ways to obtain credentials are [**brute forcing](https://en.wikipedia.org/wiki/Brute-force_attack) and [password spraying](https://owasp.org/www-community/attacks/Password_Spraying_Attack).**

## Brute Forcing and Password Spray:
- Typically, two to three attempts are safe, **provided we wait 30-60 minutes between attempts.**
- With CrackMapExec (CME) / NetExec, we can target multiple IPs, using numerous users and passwords:
	- `netexec smb 10.10.110.17 -u /tmp/userlist.txt -p 'Company01!' --local-auth`
	- `netexec smb 10.10.110.17 -u /tmp/userlist.txt -p /tmp/passlist.txt --local-auth`
	  
- **Note:** By default CME will exit after a successful login is found. **==Using the `--continue-on-success` flag will continue spraying==** even after a valid password is found.
	- If we are targetting a **non-domain joined computer, we will need to use the option `--local-auth`.**

# SMB
---
- Linux and Windows SMB servers provide different attack paths.
- When attacking a Windows SMB Server, **our actions will be limited by the privileges we had on the user we manage to compromise.**
- If this user is an **Administrator** or has specific privileges, w**e will be able to perform operations such as:**
	- Remote Command Execution
	- Extract Hashes from SAM Database
	- Enumerating Logged-on Users
	- Pass-the-Hash (PTH)

## Remote Code Execution (RCE)
- The **Windows Sysinternals website** was created in 1996 by [Mark Russinovich](https://en.wikipedia.org/wiki/Mark_Russinovich) and [Bryce Cogswell](https://en-academic.com/dic.nsf/enwiki/2358707) to offers technical resources and utilities to manage, diagnose, troubleshoot, and monitor a Microsoft Windows environment
- Sysinternals featured several **freeware tools to administer and monitor computers** running Microsoft Windows.
- One of those **==freeware tools to administer remote systems is PsExec.==**

- **PsExec** is a tool that lets us **execute processes on other systems, complete with full interactivity for console applications, without having to install client software manually.** 
- It works because it has a Windows service image inside of its executable
- It takes this **service and deploys it to the admin$ share (by default)** on the remote machine.
- It then uses the DCE/RPC interface over SMB to access the Windows Service Control Manager API. 
- Next, **it starts the PSExec service on the remote machine. The PSExec service then creates a [named pipe](https://docs.microsoft.com/en-us/windows/win32/ipc/named-pipes) that can send commands to the system.**
- ![[Pasted image 20250708071440.png]]

# Impacket PsExec
---
- To use `impacket-psexec`, we need to provide the domain/username, the password, and the IP address of our target machine. For more detailed information we can use impacket help:
	- `impacket-psexec -h`

- To **connect to a remote machine with a local administrator account**, using `impacket-psexec`, you can use the following command:
	- `impacket-psexec administrator:'Password123!'@10.10.110.17`
- The same options apply to `impacket-smbexec` and `impacket-atexec`.

# CrackMapExec / Netexec
---
- Another tool we can use to run CMD or PowerShell is `CrackMapExec`. 
- One advantage of `CrackMapExec` is the availability to run a command on multiples host at a time.
	- `netexec smb 10.10.110.17 -u Administrator -p 'Password123!' -x 'whoami' --exec-method smbexec`
	  
- **Note:** If the`--exec-method` is not defined, **CrackMapExec will try to execute the atexec method, if it fails you can try to specify the `--exec-method` smbexec.**


## Enumerating Logged-on Users
- We could use `CrackMapExec` to **enumerate logged-on users on all machines within the same network `10.10.110.17/24`**:
	- `netexec smb 10.10.110.0/24 -u administrator -p 'Password123!' --loggedon-users`

## Extract Hashes from SAM Database
- `crackmapexec smb 10.10.110.17 -u administrator -p 'Password123!' --sam`

## Pass-the-Hash (PtH)
- [[Pass the Hash (PtH)]], [[Pass the Ticket (PtT) from Linux]]
- We can use a PtH attack with any `Impacket` tool, `SMBMap`, `CrackMapExec`, among other tools. Here is an example of how this would work with `CrackMapExec`:
	- `netexec smb 10.10.110.17 -u Administrator -H 2B576ACBE6BCFDA7294D6BD18041B8FE`

# ==Forced Authentication Attacks==
---
- We can also abuse the SMB protocol by creating a **==fake SMB Server to capture users' [NetNTLM v1/v2 hashes](https://medium.com/@petergombos/lm-ntlm-net-ntlmv2-oh-my-a9b235c58ed4).==**
- The most common tool to perform such **operations is the `Responder`.** 
- [Responder](https://github.com/lgandx/Responder) is an **LLMNR, NBT-NS, and MDNS poisoner too**l with different capabilities, one of them is the possibility to set up fake services, including SMB, to **steal NetNTLM v1/v2 hashes.**:

	- `responder -I <interface name>`

![[Pasted image 20250708072006.png]]

- Suppose a user mistyped a shared folder's name `\\mysharefoder\` instead of `\\mysharedfolder\`.
- n that case, **all name resolutions will fail because the name does not exist**, and the machine will send a **multicast query to all devices on the network, including us ==running our fake SMB server.==**
	- ![[Pasted image 20250708072132.png]]
- All saved Hashes are located in Responder's logs directory (`/usr/share/responder/logs/`). We can copy the hash to a file and attempt to crack it using the hashcat module 5600.
- **Note:** If you notice **multiples hashes for one account** this is because NTLMv2 utilizes **both a client-side and server-side challenge that is randomized** for each interaction.

## Cracking Hash:
- `hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt`

## SMBRelay
---
- If we cannot crack the hash, **==we can potentially relay the captured hash to another machine using [impacket-ntlmrelayx](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ntlmrelayx.py) or Responder [MultiRelay.py](https://github.com/lgandx/Responder/blob/master/tools/MultiRelay.py).==**
- Let us see an example using `impacket-ntlmrelayx`.

- First, we need to set **SMB to `OFF` in our responder configuration file** (`/etc/responder/Responder.conf`).
- **Then we execute `impacket-ntlmrelayx` with the option `--no-http-server`, `-smb2support`, and the target machine with the option `-t`.**
- By default, `impacket-ntlmrelayx` will dump the SAM database, **but we can execute commands by adding the option `-c`**:
	- `impacket-ntlmrelayx --no-http-server -smb2support -t 10.10.110.146`

- We can create a PowerShell **reverse shell using [https://www.revshells.com/](https://www.revshells.com/), set our machine IP address, port, and the option Powershell #3 (Base64).**
- Once the victim authenticates to our server, we poison the response and make it execute our command to obtain a reverse shell.

# RPC
---
[[SMB]] -> RPC section
- In the [Footprinting module](https://academy.hackthebox.com/course/preview/footprinting), we discuss how to enumerate a machine using RPC. Apart from enumeration, we can use RPC to make changes to the system, such as:
	- Change a user's password.
	- Create a new domain user.
	- Create a new shared folder.


# Labs
---
- `smbclient ////10.129.203.6//GGJ -U anonymous` To See Shares
- Bruteforce Login by given password list:
		- `netexec smb 10.129.203.6 -u jason -p pws.list --local-auth`

# Latest SMB Vulnerabilities
---
- One recent significant vulnerability that affected the SMB protocol was called [SMBGhost](https://arista.my.site.com/AristaCommunity/s/article/SMBGhost-Wormable-Vulnerability-Analysis-CVE-2020-0796) with the [CVE-2020-0796](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2020-0796).
- The vulnerability consisted of a compression mechanism of the version SMB v3.1.1 which made Windows 10 versions 1903 and 1909 vulnerable to attack by an unauthenticated attacker.
- The vulnerability allowed the attacker to gain **remote code execution (`RCE`) and full access** to the remote target system.

## The Concept of the Attack
- In simple terms, this is an [i**nteger overflow](https://en.wikipedia.org/wiki/Integer_overflow) vulnerability in a function of an SMB driver that allows system commands to be overwritten** while accessing memory.
- The vulnerability occurs while processing a malformed compressed message after the `Negotiate Protocol Responses`.
- If the SMB server allows requests (over TCP/445), compression is generally supported, where the server and client set the terms of communication before the client sends any more data.
- Suppose the data transmitted exceeds the integer variable limits due to the excessive amount of data. In that case, these parts are written into the buffer, which leads to the **overwriting of the subsequent CPU instructions and interrupts the process's normal or planned execution**.
![[Pasted image 20250710071325.png]]