- Search for critical (red) vulnerabilities
![[Pasted image 20250324152120.png]]

# [Apache mod ssl/2.8.4  exploit](https://www.exploit-db.com/exploits/764)
---
- Potential Vulnerable to OpenLuck (Remote Buffer Overflow)

# [Samba 2.2.1a exploit](https://www.rapid7.com/db/modules/exploit/linux/samba/trans2open/)
---
- Potentially vulnerable to trans2open
- `This exploits the buffer overflow found in Samba versions 2.2.0 to 2.2.8. This particular module is capable of exploiting the flaw on x86 Linux systems that do not have the noexec stack option set. NOTE: Some older versions of RedHat do not seem to be vulnerable since they apparently do not allow anonymous access to IPC.`

# Searchsploit
---
- `searchsploit smb 2.2.1a`
- `searchsploit ssl 2.8.4`
![[Pasted image 20250324153142.png]]