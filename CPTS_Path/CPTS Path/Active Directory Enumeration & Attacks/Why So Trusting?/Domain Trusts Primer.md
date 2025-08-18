# Scenario
---
- Many large organizations will acquire new companies over time and bring them into the fold.
- One way this is done for **ease of use is to establish a trust relationship with the new domain.**
- In doing so, you can avoid migrating all the established objects, making integration much quicker.
- This trust can **also introduce weaknesses into the customer's environment** if they are not careful. A **subdomain with an exploitable flaw or vulnerability can provide us with a quick route into the target domain**

# Domain Trusts Overview
---
- **A [trust](https://social.technet.microsoft.com/wiki/contents/articles/50969.active-directory-forest-trust-attention-points.aspx) is used to establish forest-forest or domain-domain (intra-domain) authentication**, which **allows users to access resources in (or perform administrative tasks) another domain,** outside of the main domain where their account resides.
- A **trust creates a link between the authentication systems of two domains and may allow either one-way or two-way (bidirectional) communication.**

## Types of Trusts
![[Pasted image 20250814060401.png]]

## Transitive and non-transitive Trusts
- A `transitive` trust means that **trust is extended to objects that the child domain trusts.**
	- In a transitive relationship, **if `Domain A` has a trust with `Domain B`**, and **`Domain B` has a `transitive` trust with `Domain C`,** then **`Domain A` will automatically trust `Domain C`.**
- In a `non-transitive trust`, t**he child domain itself is the only one trusted.**
- ![[Pasted image 20250814060536.png]]

## Directional Trust
Trusts can be set up in two directions: one-way or two-way (bidirectional).
- `One-way trust`: **Users in a `trusted` domain can access resources in a trusting domain, not vice-versa.**
- `Bidirectional trust`: **Users from both trusting domains can access resources in the other domain**. 
- For example, in a bidirectional trust between **`INLANEFREIGHT.LOCAL` and `FREIGHTLOGISTICS.LOCAL`, users in `INLANEFREIGHT.LOCAL` would be able to access resources in `FREIGHTLOGISTICS.LOCAL`, and vice-versa**

![[Pasted image 20250814060656.png]]

# Enumerating Trust Relationships
---
- We can use the [Get-ADTrust](https://docs.microsoft.com/en-us/powershell/module/activedirectory/get-adtrust?view=windowsserver2022-ps) cmdlet to enumerate domain trust relationships.

## Using Get-ADTrust
- `Import-Module activedirectory`
- `Get-ADTrust -Filter *`

- The above output shows that our current domain **`INLANEFREIGHT.LOCAL` has two domain trusts**. 
	- The first is with **`LOGISTICS.INLANEFREIGHT.LOCAL`, and the `IntraForest` property shows that this is a child domain, and we are currently positioned in the root domain of the forest.**
	- The second trust is with the **domain `FREIGHTLOGISTICS.LOCAL,`** and the **`ForestTransitive` property is set to `True`, which means that this is a forest trust or external trust.**
- ![[Pasted image 20250814060859.png]]

## Checking for Existing Trusts using Get-DomainTrust
- `Get-DomainTrust `
- ![[Pasted image 20250814060948.png]]
## Using Get-DomainTrustMapping
- `Get-DomainTrustMapping`
- ![[Pasted image 20250814061044.png]]

## Checking Users in the Child Domain using Get-DomainUser
- `Get-DomainUser -Domain LOGISTICS.INLANEFREIGHT.LOCAL | select SamAccountName`
- ![[Pasted image 20250814061102.png]]

## Using netdom to query domain trust
- `netdom query /domain:inlanefreight.local trust`
- ![[Pasted image 20250814061122.png]]
## Using netdom to query domain controllers
- `netdom query /domain:inlanefreight.local dc`
## Using netdom to query workstations and servers
- `Netdom query /domain:inlanefreight.local workstation`
![[Pasted image 20250814061221.png]]
- We can also use **BloodHound to visualize these trust relationships by using the `Map Domain Trusts` pre-built query.**