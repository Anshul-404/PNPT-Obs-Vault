# AD DS SCHEMA
---
- Think it as a rule book or a **blueprint**.
- It **==defines every type of object that can be stored==** in the directory.
- **==Enforces rules regarding object==** creation and configuration.
- ![[Pasted image 20250326154611.png]]
# Domains
---
- Domains are used to **group and manage objects** in an organization.
- An admin **boundary for our policies to the objects** inside this domain.
- ![[Pasted image 20250326154932.png]]
# Trees
---
- **Hierarchy** of domains in AD DS.
- **Parent and children** relationship.
- ![[Pasted image 20250326155043.png]]
# Forests
---
- **Bunch of trees (one or more domain tress) form a Forest.**
- They share **enterprise and schema admins**.
- We might **==cross forests with enterprise admins==** if we have access.
  
- ![[Pasted image 20250326155208.png]]

# Organizations Units (OUs)
---
- ==**Containers**==
- They have **users, groups, devices**, other OUs, etc.
- We can **assign different permissions and policies** to these OUs.
- For example, ==**applying policies to a set of computers in the same domain**==, we can use containers.

# Trusts
---
![[Pasted image 20250326155756.png]]

# Objects
---
- ==**Objects live with OUs**==
- Users, groups, computers, etc
- ![[Pasted image 20250326160004.png]]


### **Key Takeaway**

Objects **exist inside a domain**, but **can be placed inside OUs for better management**.

- **Domain = The house** 🏠
    
- **OUs = Different rooms in the house** 📂
    
- **Objects = People & furniture inside those rooms** 🛋️