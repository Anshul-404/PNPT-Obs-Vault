=> Our client wants to know if we can identify which operating system their provided machine is running on. Submit the OS name as the answer.
- `nmap 10.129.143.222 -O  -sC` -> Ubuntu

=> After the configurations are transferred to the system, our client wants to know if it is possible to find out our target's DNS server version. Submit the DNS server version of the target as the answer. 
- `nmap 10.129.143.214 -p53 -sU -sV` -> `-sU` for UDP scan

=> Now our client wants to know if it is possible to find out the version of the running services. Identify the version of service our client was talking about and submit the flag as the answer.
