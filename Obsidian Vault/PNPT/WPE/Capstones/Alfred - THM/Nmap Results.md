```
Starting Nmap 7.95 ( https://nmap.org ) at 2025-04-20 15:02 EDT
Nmap scan report for 10.10.146.160
Host is up (0.14s latency).
Not shown: 65532 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 7.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Microsoft-IIS/7.5
3389/tcp open  ms-wbt-server Microsoft Terminal Service
| rdp-ntlm-info: 
|   Target_Name: ALFRED
|   NetBIOS_Domain_Name: ALFRED
|   NetBIOS_Computer_Name: ALFRED
|   DNS_Domain_Name: alfred
|   DNS_Computer_Name: alfred
|   Product_Version: 6.1.7601
|_  System_Time: 2025-04-20T19:06:32+00:00
|_ssl-date: 2025-04-20T19:06:37+00:00; -12s from scanner time.
| ssl-cert: Subject: commonName=alfred
| Not valid before: 2025-04-19T18:53:22
|_Not valid after:  2025-10-19T18:53:22
8080/tcp open  http          Jetty 9.4.z-SNAPSHOT
|_http-title: Site doesn't have a title (text/html;charset=utf-8).
|_http-server-header: Jetty(9.4.z-SNAPSHOT)
| http-robots.txt: 1 disallowed entry 
|_/
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|specialized|phone
Running (JUST GUESSING): Microsoft Windows 2008|7|Phone|Vista (92%)
OS CPE: cpe:/o:microsoft:windows_server_2008:r2 cpe:/o:microsoft:windows_7 cpe:/o:microsoft:windows_8 cpe:/o:microsoft:windows cpe:/o:microsoft:windows_vista
Aggressive OS guesses: Microsoft Windows 7 or Windows Server 2008 R2 (92%), Microsoft Windows Server 2008 R2 SP1 (87%), Microsoft Windows Server 2008 (86%), Microsoft Windows Server 2008 R2 or Windows 7 SP1 (86%), Microsoft Windows Server 2008 R2 or Windows 8 (86%), Microsoft Windows Embedded Standard 7 (86%), Microsoft Windows 8.1 Update 1 (86%), Microsoft Windows Phone 7.5 or 8.0 (86%), Microsoft Windows Vista or Windows 7 (85%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -12s, deviation: 0s, median: -12s

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   137.93 ms 10.8.0.1
2   140.48 ms 10.10.146.160

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 260.57 seconds
```