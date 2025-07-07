- To effectively understand attacks on the different services, we should look at **how these services can be attacked**.

# The Concept of Attacks
---
![[Pasted image 20250707055515.png]]

- The concept is based on four categories that occur for each vulnerability.

# Source
---
- We can generalize `Source` as a **source of information used for the specific task of a process.**
- There are many different ways to pass information to a process.
- ![[Pasted image 20250707063054.png]]
- The source is, therefore, **the source that is exploited for vulnerabilities.**

## Log4j
- A great example is the critical Log4j vulnerability ([CVE-2021-44228](https://cve.mitre.org/cgi-bin/cvename.cgi?name=cve-2021-44228)) which was published at the end of 2021.
- Log4j is a framework or `Library` used to **log application messages in Java** and other programming languages.
- In this example, an attacker can **manipulate the HTTP User-Agent header and insert a JNDI lookup as a command** intended for the Log4j `library`
- Accordingly, not the actual User-Agent header, such as Mozilla 5.0, is processed, **but the JNDI lookup.**


# Processes
---
- The `Process` is about **processing the information forwarded from the source.**
- These are processed according to the intended task determined by the program code. For each task, the **developer specifies how the information is processed.**
- ![[Pasted image 20250707064052.png]]
## Log4j
- The process of Log4j is to log the **User-Agent as a string using a function** and store it in the designated location. 
- The vulnerability in this process is the **misinterpretation of the string, which leads to the execution of a request** instead of logging the events.

# Privileges
---
- `Privileges` are present in any system that **controls processes**. These serve as a **type of permission that determines what tasks and actions can be performed on the system.** 
- If the process satisfies these privileges and conditions, the system approves the action requested.
- ![[Pasted image 20250707064224.png]]
## Log4j
- What made the Log4j vulnerability so dangerous was the `Privileges` that the implementation brought.
- Logs are often considered **sensitive because they can contain data about the service, the system itself, or even customers.**
- Accordingly, most applications with the Log4j implementation **were run with the privileges of an administrator.**

# Destination
---
- Every **task has at least one purpose and goal** that must be fulfilled.
- Logically, if any data set changes **were missing or not stored or forwarded** anywhere, **the task would be generally unnecessary.**
- Therefore we speak here of the `Destination` where the changes will be made.
- ![[Pasted image 20250707064344.png]]
## Log4j
- The misinterpretation of the User-Agent leads to a **JNDI lookup** which is executed as a command from the system with administrator privileges and queries a remote server controlled by the attacker, which **in our case is the `Destination` in our concept of attacks.**

![[Pasted image 20250707064434.png]]