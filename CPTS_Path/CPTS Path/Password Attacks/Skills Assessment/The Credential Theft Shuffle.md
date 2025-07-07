- [The Credential Theft Shuffle](https://adsecurity.org/?p=2362), as coined by `Sean Metcalf`, is a systematic approach attackers use to compromise Active Directory environments by exploiting `stolen credentials`.
- The process begins with gaining initial access, often through phishing, followed by obtaining local administrator privileges on a machine. 
- Attackers t**hen extract credentials from memory using tools like Mimikatz and leverage these credentials to `move laterally across the network`.**
- Techniques such as **pass-the-hash (PtH) and tools like NetExec** facilitate this lateral movement and further credential harvesting. 
- The ultimate goal is to escalate privileges and `gain control over the domain`, often by **compromising Domain Admin accounts** or performing DCSync attacks. 
- Sean emphasizes the importance of implementing security measures such as the `Local Administrator Password Solution (LAPS)`, enforcing `multi-factor authentication`, and `restricting administrative privileges` to mitigate such attacks.

# Skills Assessment
---
- **==`Betty Jayde` works at `Nexura LLC`.==** 
- We know she uses the password ==`Texas123!@#`== on multiple websites, and we believe she may reuse it at work. 
	- `Betty Jayde`: `Texas123!@#`
- Infiltrate Nexura's network and gain command execution on the **domain controller.** The following hosts are in-scope for this assessment:
- ![[Pasted image 20250706054024.png]]

# Pivoting Primer
---
- The internal hosts (`JUMP01`, `FILE01`, `DC01`) reside on a private subnet that is not directly accessible from our attack host.
- he only externally reachable system is `DMZ01`, which has a second interface connected to the internal network.
- To access these internal systems, **we must first gain a foothold on `DMZ01`. From there, we can `pivot`** — that is, route our traffic through the compromised host into the private network.
- After compromising the DMZ, refer to the module `cheatsheet` for the necessary commands to set up the pivot and continue your assessment.