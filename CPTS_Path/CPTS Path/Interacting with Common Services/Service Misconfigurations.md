- Misconfigurations usually happen when a system administrator, technical support, or developer does not correctly configure the security framework of an application, website, desktop, or server leading to dangerous open pathways for unauthorized users.

# Authentication
---
- In previous years (though we still see this sometimes during assessments), it was widespread for **services to include default credentials** (username and password).
- This presents a security issue **because many administrators leave the default credentials unchanged.**
- Nowadays, most software asks users to set up credentials upon installation.
	- ![[Pasted image 20250707065504.png]]
## Anonymous Authentication
- Another misconfiguration that can exist in common services is **anonymous authentication.** 
- The service can be configured to allow anonymous authentication, **allowing anyone with network connectivity to the service without being prompted for authentication.**

## Misconfigured Access Rights
- Misconfigured access rights are when **user accounts have incorrect permissions.** 
- The bigger problem could be giving **people lower down the chain of command access to private information that only managers or administrators should have.**
- Administrators need to plan their access rights strategy, and there are some alternatives such as [Role-based access control (RBAC)](https://en.wikipedia.org/wiki/Role-based_access_control), [Access control lists (ACL)](https://en.wikipedia.org/wiki/Access-control_list). 

# Unnecessary Defaults
---
- The initial configuration of devices and software may include but is not limited to settings, features, files, and credentials.
- Those default values are **usually aimed at usability** rather than security.
- Unnecessary defaults are **those settings we need to change to secure a system by reducing its attack surface.**
- ![[Pasted image 20250707065902.png]]

# Preventing Misconfiguration
---
- Once we have figured out our environment, the most straightforward strategy to control risk is to lock down the most critical infrastructure and only allow desired behavior. 
- Any communication that is not required by the program should be disabled. This may include things like:
	- ![[Pasted image 20250707065943.png]]
	- 