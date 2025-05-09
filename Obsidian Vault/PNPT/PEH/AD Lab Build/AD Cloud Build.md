- Since do not have good computer, opted for `Azure` lab build.
- Should be a free (cheap) build.
- [Reference](https://kamran-bilgrami.medium.com/ethical-hacking-lessons-building-free-active-directory-lab-in-azure-6c67a7eddd7f)
- IP : `10.0.1.4`
- DC creds: `kamran : Password1234`
- Connect to DC using:
	- `xfreerdp3 /v:10.0.1.4 /u:kamran /p:Password1234`
	- After setup `xfreerdp3 /v:10.0.1.4 /u:"MARVEL\kamran" /p:Password1234`

- **IP Address of domain machine**
---
![[Pasted image 20250327112233.png]]
- First User machine (The Punisher) : 
	- `fcastle : myPassword01`
	- IP : `10.0.1.9`
	- For Local User:
		- `xfreerdp3 /v:10.0.1.9  /u:fcastle /p:myPassword01`
	- For Domain user:
		- `xfreerdp3 /v:10.0.1.9  /u:'MARVEL\fcastle' /p:Password1`
- Second User machine (Spiderman):
	- `pparker : myPassword02`
	- IP: 10.0.1.10
	- For Local User:
		- `xfreerdp3 /v:10.0.1.10 /u:pparker /p:myPassword02`
	- For Domain user:
		- `xfreerdp3 /v:10.0.1.10  /u:'MARVEL\pparker' /p:Password1`
- Local `Administrator : Password1!`
# OpenVPN Setup
---
- Instead of relying on public IPs, I added an `Ubuntu` VM with public IP, set it up as OpenVPN Server, now I can directly access any machine from my network without any costs.
- [Reference](https://gist.github.com/proffapt/15cacf6c0abdd5509e5c1b7d2c7a49ce)
- OpenVPN Server Public IP : `REDACTED`
- Credentials: `drasstrange`: `Password!123`
- We have to configure `Routing Table` and `Route Path` so that AD machines can reach another network i.e `OpenVPN Network`:
![[Pasted image 20250328064533.png]]