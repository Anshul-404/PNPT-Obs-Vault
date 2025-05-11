# Domain Controller
---
- `KamerTaj-DC` : `10.0.1.4`
- `anshul`: `Password1234`
- `xfreerdp3 /v:10.0.1.4 /u:"MARVEL\anshul" /p:Password1234`

# Portal
---
- `drasstrange`: `Password!123`: `192.168.1.4`: `10.8.0.1`: `public_ip_redacted`
- `wong`: `Password!123`: `192.168.1.5`

# User machines
---
- `DrStrange`: `10.0.1.7`
	- `sstrange`: `myPassword01` => Local Password
	- `MARVEL\sstrange` : `Password1`
	- `xfreerdp3 /v:10.0.1.7 /u:sstrange /p:myPassword01`
	- `xfreerdp3 /v:10.0.1.7 /u:"MARVEL\sstrange" /p:Password1`
- `ScarletWitch`: `10.0.1.8`
	- `wmaximoff`: `myPassword02`
	- `MARVEL\wmaximoff`: `Password1`
	- `xfreerdp3 /v:10.0.1.8 /u:wmaximoff /p:myPassword02`
	- `xfreerdp3 /v:10.0.1.8 /u:"MARVEL\wmaximoff" /p:Password1`
# Routing to VPN
---
- We have to configure `Routing Table` and `Route Path` so that AD machines can reach another network i.e `OpenVPN Network`:
- Create a "Routing Table" then follow below to add a route
![[Pasted image 20250328064533.png]]