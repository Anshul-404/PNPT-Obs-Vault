- Something that connects to **LDAP or SMB connection**, something along these lines.
- [Reference](https://www.mindpointgroup.com/blog/how-to-hack-through-a-pass-back-attack/)
![[Pasted image 20250329031434.png]]
- If we can change the LDAP server address **(most of the times pointing to DC), to our IP address**
- We can setup a `nc` listener or even **responder**, it would capture the traffic
![[Pasted image 20250329031658.png]]


- With **SMTP**:
	- ![[Pasted image 20250329031747.png]]