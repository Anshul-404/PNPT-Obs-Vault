- Apart from **web applications**, other services include (but are not limited to) **`FTP`, `SMB`, `NFS`, `IMAP/POP3`, `SSH`, `MySQL/MSSQL`, `RDP`, `WinRM`, `VNC`, `Telnet`, `SMTP`, and `LDAP`.**
- For further reading on many of these services, see the [Footprinting](https://academy.hackthebox.com/course/preview/footprinting) module : [[Footprinting_Module_Cheat_Sheet.pdf]]
- The most common services suitable to manage a Windows server over the network are **`RDP`, `WinRM`, and `SSH`.**

# WinRM - `5985` (`HTTP`),`5986` (`HTTPS`).
---
- [Windows Remote Management](https://docs.microsoft.com/en-us/windows/win32/winrm/portal) (`WinRM`) is the Microsoft implementation of the [Web Services Management Protocol](https://docs.microsoft.com/en-us/windows/win32/winrm/ws-management-protocol) (`WS-Management`).
- It is a **network protocol based on XML web services** using the [Simple Object Access Protocol](https://docs.microsoft.com/en-us/windows/win32/winrm/windows-remote-management-glossary) (`SOAP`) used for remote management of Windows systems.
- It takes care of the **communication between [Web-Based Enterprise Management](https://en.wikipedia.org/wiki/Web-Based_Enterprise_Management) (`WBEM`) and the [Windows Management Instrumentation](https://docs.microsoft.com/en-us/windows/win32/wmisdk/wmi-start-page) (`WMI`),** which can call the [Distributed Component Object Model](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-dcom/4a893f3d-bd29-48cd-9f43-d9777a4415b0) (`DCOM`).

- For security reasons, **==WinRM must be activated and configured==** manually in Windows 10/11.

## NetExec
- NetExec Menu Options:
	- `netexec -h`
		- ![[Pasted image 20250629050125.png]]
- Usage:
	- `netexec <proto> <target-IP> -u <user or userlist> -p <password or passwordlist>`
	- `netexec winrm 10.129.42.197 -u user.list -p password.list`
- The appearance of `(Pwn3d!)` is the sign that we can most likely execute system commands if we log in with the brute-forced user.

## Evil-WinRM
- Usage: 
	- `evil-winrm -i <target-IP> -u <username> -p <password>`
	- `evil-winrm -i 10.129.42.197 -u user -p password`

# SSH - TCP/22
---
- Secure Shell (SSH) is a more secure way to connect to a remote host to execute system commands or transfer files from a host to a server.
- This service uses **three different cryptography operations/methods**: `symmetric` encryption, `asymmetric` encryption, and `hashing`.

## Symmetric Encryption
- Symmetric encryption uses the **`same key` for encryption and decryption.**
- The [Diffie-Hellman key exchange](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange) method is used for this purpose.
- If a **third party obtains the key, it cannot decrypt the messages** because the key ==**exchange method is unknown.**==

## Asymmetric Encryption
- Asymmetric encryption uses `two keys`: **a private key and a public key.** 
- The **private key must remain secret** because only it can decrypt the messages that have been encrypted with the public key.
- If an attacker **obtains the private key**, which is often not password protected, he **==will be able to log in to the system without credentials.==**
- Once a connection is established, the server uses the public key for initialization and authentication

## Hashing
- The hashing method converts the t**ransmitted data into another unique value**. SSH uses **hashing to confirm the authenticity of messages.** 

## Hydra - SSH
- We can use a tool like Hydra to brute force SSH:
	- `hydra -L user.list -P password.list ssh://10.129.42.197`

# Remote Desktop Protocol (RDP) - TCP/3389
---
- Microsoft's [Remote Desktop Protocol](https://docs.microsoft.com/en-us/troubleshoot/windows-server/remote/understanding-remote-desktop-protocol) (`RDP`) is a network protocol that allows **remote access to Windows systems** via `TCP port 3389` by default.
- RDP provides both users and administrators/support staff with remote access to Windows hosts within an organization.

## Hydra - RDP
- `hydra -L user.list -P password.list rdp://10.129.42.197`

# SMB - TCP/139, TCP/445
---
- [Server Message Block](https://docs.microsoft.com/en-us/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) (`SMB`) is a protocol responsible for **transferring data between a client and a server in local area networks.** 
- It is used to **implement file and directory sharing and printing services** in Windows networks.
- SMB is also known as [Common Internet File System](https://cifs.com/) (`CIFS`). 
- It is part of the SMB protocol and **enables universal remote connection of multiple platforms** such as Windows, Linux, or macOS.
- In addition, we will often encounter **[Samba](https://wiki.samba.org/index.php/Main_Page), which is an open-source implementation** of the above functions.

## Hydra - SMB
- `hydra -L user.list -P password.list smb://10.129.42.197`
- ![[Pasted image 20250629051212.png]]

## Metasploit Framework
- `use auxiliary/scanner/smb/smb_login`

## Netexec
---
- We can use `NetExec` again to view the available shares and what privileges we have for them:
	- `netexec smb 10.129.42.197 -u "user" -p "password" --shares`

## Smbclient
---
- `smbclient -U user \\\\10.129.42.197\\SHARENAME`


# Labs
---
	=> Find the user for the WinRM service and crack their password. Then, when you log in, you will find the flag in a file there. Submit the flag you found as the answer.