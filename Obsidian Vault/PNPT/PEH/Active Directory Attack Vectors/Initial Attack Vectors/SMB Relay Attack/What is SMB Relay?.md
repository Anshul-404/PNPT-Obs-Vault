- Instead of cracking the hashes, we can utilize `SMB Relay` to potentially gain **access to ==another machine on the network== using those hashes**.
- Note: We are **relaying these hashes** to another machine to login, not the same machine
- This is why, the user needs to be on both the machines as admin.
	- The **other machine verifies the credentials from its own NTLM db**. 
![[Pasted image 20250328144450.png]]
![[Pasted image 20250328154354.png]]