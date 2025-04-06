# Nmap Scan
---
```
drasstrange@ip-10-200-107-33:~$ nmap -sV -T4 10.200.107.31
Starting Nmap 7.80 ( https://nmap.org ) at 2025-04-05 15:28 UTC
Stats: 0:01:10 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 87.50% done; ETC: 15:30 (0:00:10 remaining)
Nmap scan report for ip-10-200-107-31.eu-west-1.compute.internal (10.200.107.31)
Host is up (0.00035s latency).
Not shown: 992 closed ports
PORT     STATE SERVICE       VERSION
22/tcp   open  ssh           OpenSSH for_Windows_7.7 (protocol 2.0)
80/tcp   open  http          Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.4.11)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
443/tcp  open  ssl/http      Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.4.11)
445/tcp  open  microsoft-ds?
3306/tcp open  mysql?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

