# Manual Enumeration
---
- **==If Anonymous LDAP Binds Are Allowed==**, we can use `ldapsearch`:
	`ldapsearch -x -H ldap://<DC_IP> -b "dc=domain,dc=com"`
- If **Anonymous ==Binds Are Blocked==,** we'll get:
	- ==`ldap_bind: Invalid credentials (49)`==
	- At that point, you’ll need:
		- **Valid creds** (even low-priv)
		- OR exploit a system for access
		- OR find cleartext/hashes elsewhere (e.g., SMB null sessions, unauth file shares)
- For filtering only the account sAMAccountName and userPrincipalName:
```bash
ldapsearch -x -H ldap://<DC-IP> -b "dc=<DOMAIN-BEFORE-DOT>,dc=<DOMAIN-AFTER-DOT>" "(objectClass=user)" sAMAccountName userPrincipalName | \
awk '/^sAMAccountName:/ || /^userPrincipalName:/ {print $0}'
```
- For example:
	- ![[Pasted image 20250424224054.png]]

# Using `enum4linux`
---
- `enum4linux <DC-IP`
	![[Pasted image 20250424224203.png]]
# What Next?
---
- Try `ASREP-Roasting`, then `Kerberoasting`:
	- [[ASREP Roasting Lab]]
	- [[Kerberoasting Lab]]