- Transferred the `nmap` binary on compromised server
- Direct nmap even after pivoting through`sshuttle` is not possible
- `/nmap_drasstrange 10.200.81.150 -T4`

```
Nmap scan report for ip-10-200-81-150.eu-west-1.compute.internal (10.200.81.150)
Cannot find nmap-mac-prefixes: Ethernet vendor correlation will not be performed
Host is up (-0.0022s latency).
Not shown: 6147 filtered ports
PORT     STATE SERVICE
80/tcp   open  http
3389/tcp open  ms-wbt-server
5985/tcp open  wsman
MAC Address: 02:EC:51:2A:3F:03 (Unknown)
```

