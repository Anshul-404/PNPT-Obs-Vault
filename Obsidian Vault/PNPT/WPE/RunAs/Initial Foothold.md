# Port 80
---
![[Pasted image 20250416144910.png]]
- Cannot find much

# Port 21 - FTP
---
- **Anonymous login** permitted but
- Hangs in passive mode:
	- ![[Pasted image 20250416145734.png]]
- Therefore using **Active mode**:
	- `ftp 10.10.10.98 -A`
	- ![[Pasted image 20250416145818.png]]
	- Get the files:
		- Need to set backups/backup.mdb **mode to `binary`** for this file
			- ![[Pasted image 20250416150059.png]]
		- Engineer/Access Control.zip
			- ![[Pasted image 20250416150155.png]]

# FTP Files
---
- **Access Control.zip**
	- Cannot run unzip as it is compressed using 7z and gives an error, therefore with 7z:
		- A password is required
		- ![[Pasted image 20250416150616.png]]
	
- **backup.mdb**:
	- **Microsoft Access database** file, viewed using: https://mdbviewer.herokuapp.com/
	- Table `auth_user` found with **credentials**:
		- ![[Pasted image 20250416151507.png]]
	- Credentials:
		- `admin : admin`
		- `engineer : access4u@security`
		- `backup_admin : admin`

# Cracking Access Control.zip
---
- Try cracking using `zip2john Access Control.zip > hash`
- `john hash --wordlist=passwords.txt` => **List of above passwords**
	- ![[Pasted image 20250416151743.png]]
- ![[Pasted image 20250416151845.png]]
- Opening the **Access Control.pst** using https://goldfynch.com/pst-viewer/:
	- ![[Pasted image 20250416152109.png]]
- Credentials:
	- `security` : `4Cc3ssC0ntr0ller`

# Port 23 - telnet
---
![[Pasted image 20250416152234.png]]
- Asks for login name, let's try previous set of credentials:		- `security` : `4Cc3ssC0ntr0ller`
- ![[Pasted image 20250416152423.png]]
