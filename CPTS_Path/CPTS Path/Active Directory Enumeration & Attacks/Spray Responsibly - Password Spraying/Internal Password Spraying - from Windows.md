- From a foothold on a domain-joined Windows host, t**he [DomainPasswordSpray](https://github.com/dafthack/DomainPasswordSpray) tool is highly effective**
- If we are **authenticated to the domain**, **the tool will automatically generate a user list from Active Directory**, query the domain password policy, and exclude user accounts within one attempt of locking out.

# Using DomainPasswordSpray.ps1
---
- Since the host is domain-joined, **we will skip the `-UserList` flag and let the tool generate a list for us.** 
- We'll **supply the `Password` flag and one single password** and then use **the `-OutFile` flag to write our output to a file** for later use.

	- `Import-Module .\DomainPasswordSpray.ps1`
	- `Invoke-DomainPasswordSpray -Password Welcome1 -OutFile spray_success -ErrorAction SilentlyContinue`
	- ![[Pasted image 20250807062311.png]]
	- We could **also utilize Kerbrute to perform the same user enumeratio**n and spraying steps shown in the previous section.

# Mitigations
----
![[Pasted image 20250807062350.png]]
# Other Considerations
---
- It is vital to ensure that your **domain password lockout policy doesn’t increase the risk of denial of service attacks**. 
- If it is very restrictive and requires an administrative intervention to unlock accounts manually, **a careless password spray may lock out many accounts within a short period.**

# Detection
---
- Some indicators of external password spraying attacks include many account lockouts in a short period, server or application logs showing many login attempts with valid or non-existent users, or many requests in a short period to a specific application or URL
- In the Domain Controller’s security log, many instances of **event ID [4625: An account failed to log on](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4625) over a short period may indicate a password spraying attack.**
- Organizations should also **monitor event ID [4771: Kerberos pre-authentication failed](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4771), which may indicate an LDAP password spraying attempt.**
- To do so, they will need to enable Kerberos logging.

# External Password Spraying
---
- More in **Password Spraying section in PNPT notes**
![[Pasted image 20250807062540.png]]

# LAB
---
=> Using the examples shown in this section, find a user with the password Winter2022. Submit the username as the answer.
- ![[Pasted image 20250807062918.png]]
- ![[Pasted image 20250807062944.png]]