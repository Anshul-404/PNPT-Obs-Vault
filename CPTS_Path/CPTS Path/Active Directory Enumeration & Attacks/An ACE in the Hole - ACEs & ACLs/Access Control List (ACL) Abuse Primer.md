- For security reasons, **not all users and computers in an AD environment can access all objects and files**.
- These types of **==permissions are controlled through Access Control Lists (ACLs).==**

# Access Control List (ACL) Overview
---
- In their simplest form, **ACLs are lists that define** 
	- a) ==**who has access to which asset/resource**== and 
	- b) the ==**level of access**== they are provisioned.
- The **==settings themselves in an ACL are called `Access Control Entries` (`ACEs`).==**
- Each **ACE maps back to a user, group, or process** (==also known as security principals==) and **defines the rights granted to that principal**
- Every **object has an ACL, but can have multiple ACEs** because multiple security principals can access objects in AD.

## Types of ACLs
---
- **Discretionary Access Control List (DACL)** - 
	- defines **==which security principals are granted or denied access to an object==**. 
	- **DACLs are made up of ACEs that either allow or deny access**. 
	- When someone attempts to access an object, **==the system will check the DACL for the level of access that is permitted==**. 
	- If a **==DACL==** **==does not exist for an object, all who attempt to access the object are granted full rights.==** 
	- If a **==DACL exists, but does not have any ACE entries specifying specific security settings, the system will deny access to all users,==** groups, or processes attempting to access it.

- **System Access Control Lists (SACL)** - 
	- allow administrators to log access attempts made to secured objects.

- We see the **ACL for the user account `forend` in the image below.**
- Each **item under `Permission entries` makes up the `DACL` for the user account**, while the **individual entries (such as `Full Control` or `Change Password`) are ACE entries** showing rights granted over this user object to various users and groups.
		![[Pasted image 20250810053431.png]]
- Viewing the SACLs through the Auditing Tab

# Access Control Entries (ACEs)
---
- **Access Control Lists (ACLs) contain ACE entries that name a user or group and the level of access they have** over a given securable object
- There are `three` main types of ACEs that can be applied to all securable objects in AD:
	- ![[Pasted image 20250810054843.png]]
- Each ACE is made up of the following `four` components:

	1. The **security identifier (SID) of the user/group that has access to the object** (or principal name graphically)
	2. A **flag denoting the type of ACE** (access denied, allowed, or system audit ACE)
	3. A **set of flags that specify whether or not child containers/objects can inherit the given ACE entry from the primary or parent object**
	4. An **[access mask](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-dtyp/7a53f60e-e730-4dfe-bbe9-b21b62eb790b?redirectedfrom=MSDN) which is a 32-bit value that defines the rights granted to an object**
1. We can **view this graphically in `Active Directory Users and Computers` (`ADUC`).**

# ==Why are ACEs Important?==
---
- **Attackers utilize ACE entries to either further access or establish persistence.**
- These can be great for us as penetration testers as **many organizations are unaware of the ACEs applied to each object or the impact that these can have** if applied incorrectly.
- They **cannot be detected by vulnerability scanning tools,** and often go **unchecked for many years,** especially in large and complex environments.
- During an assessment where the client has taken care of all of the "low hanging fruit" AD flaws/misconfigurations, ACL abuse can be a great way for us to move laterally/vertically and even achieve full domain compromise. 
- ==These can be enumerated (and visualized) using a tool such as BloodHound, **and are all abusable with PowerView, among other tools==:**
	- ![[Pasted image 20250810055117.png]]

# ACL Attacks in the Wild
---
We can use ACL attacks for:
- Lateral movement
- Privilege escalation
- Persistence

![[Pasted image 20250810055239.png]]