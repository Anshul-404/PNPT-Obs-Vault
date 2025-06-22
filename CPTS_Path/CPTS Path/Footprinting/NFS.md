![[Pasted image 20250615193948.png]]
- The most common authentication is via UNIX `UID`/`GID` and `group memberships`, which is why this syntax is most likely to be applied to the NFS protocol.
- One problem is that the client and server do not necessarily have to have the same mappings of UID/GID to users and groups, and the server does not need to do anything further.
- This is why NFS should only be used with this authentication method in trusted networks.

# Default Configuration
---
- `cat /etc/exports `
- ![[Pasted image 20250615194155.png]]

# Dangerous Settings
---
![[Pasted image 20250615194305.png]]

# Footprinting
---
- `nmap 10.129.14.128 -p111,2049 -sV -sC`
- The **rpcinfo NSE script** retrieves a list of all **==currently running RPC services, their names and descriptions, and the ports they use.==** 
	- `nmap --script nfs* 10.129.14.128 -sV -p111,2049`

# Show and Mount
---
- `showmount -e 10.129.14.128`
- `mkdir target-NFS`
- `sudo mount -t nfs 10.129.14.128:/ ./target-NFS/ -o nolock`
- `cd target-NFS`
- Unmounting: `sudo umount ./target-NFS`

# Escalation
---
- If we have access to the system via **SSH** and want to read files from another folder that a specific user can read, **we would need to upload a shell to the NFS share that has the SUID of that user** and then **run the shell via the SSH user.**