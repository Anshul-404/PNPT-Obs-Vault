- A `Payload` in Metasploit refers to a **module that aids the exploit module in (typically) returning a shell to the attacker.** 
- The payloads are sent together with the exploit itself to b**ypass standard functioning procedures of the vulnerable service** (`exploits job`) and then run **on the target OS to typically return a reverse connection to the attacker** and establish a foothold (`payload's job`).

There are three different types of payload modules in the Metasploit Framework: Singles, Stagers, and Stages.

# Singles
---
- A `Single` payload **contains the exploit and the entire shellcode for the selected task**. Inline payloads are by design **more stable** than their counterparts because they contain everything all-in-one. 
- However, **some exploits will not support** the resulting size of these payloads as they **can get quite large**. `Singles` are self-contained payloads. 
-  A Single payload can be as **simple as adding a user to the target system or booting up a process.**

# Stagers
---
- `Stager` payloads **work with** **Stage payloads to perform a specific task.** 
- **A Stager is waiting on the attacker machine, ready to establish a connection to the victim host once the stage completes its run on the remote host.** 
- `Stagers` are typically used to set up a network connection between the attacker and victim and are designed to be small and reliable.

# Stages
---
- `Stages` are pa**yload components that are downloaded by stager's modules.** 
- The various payload Stages provide advanced features with no size limits, such as Meterpreter, VNC Injection, and others.

# MSF - Staged Payloads
---
- A staged payload is, simply put, an `exploitation process` that is modularized and functionally separated to help segregate the different functions it accomplishes into different code blocks.
- The scope of this payload, as with any others, besides granting shell access to the target system, is to be as compact and inconspicuous as possible to aid with the Antivirus (`AV`) / Intrusion Prevention System (`IPS`) evasion as much as possible.
- **`Stage0` of a staged payload represents the initial shellcode sent over the network to the target machine's vulnerable service, which has the sole purpose of initializing a connection back to the attacker machine.**

# Meterpreter Payload
---
- The `Meterpreter` payload is a specific type of **multi-faceted payload that uses `DLL injection` to ensure the connection to the victim host is stable,** hard to detect by simple checks, and **persistent across reboots or system changes.**
- Meterprete**r resides completely in the memory of the remote host** and leaves no traces on the hard drive, making it very **difficult to detect with conventional forensic techniques.**

# Searching for Payloads
---
- `show payloads`

# MSF - Searching for Specific Payload
---
- `grep meterpreter show payloads`
- `grep meterpreter grep reverse_tcp show payloads`