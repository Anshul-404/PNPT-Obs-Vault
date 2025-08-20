```
nmap -p- -T4 -oN nmap/nmap_internal_scan 10.10.10.0/24

Nmap scan report for mail (10.10.10.5)
Host is up (0.000060s latency).
Not shown: 65526 closed ports
PORT    STATE SERVICE
22/tcp  open  ssh
25/tcp  open  smtp
80/tcp  open  http
110/tcp open  pop3
143/tcp open  imap2
443/tcp open  https
587/tcp open  submission
993/tcp open  imaps
995/tcp open  pop3s

Nmap scan report for 10.10.10.15
Host is up (0.0034s latency).
Not shown: 65523 closed ports
PORT      STATE SERVICE
135/tcp   open  loc-srv
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
5040/tcp  open  unknown
49664/tcp open  unknown
49665/tcp open  unknown
49666/tcp open  unknown
49667/tcp open  unknown
49668/tcp open  unknown
49669/tcp open  unknown
49670/tcp open  unknown
49676/tcp open  unknown

Nmap scan report for 10.10.10.25
Host is up (0.0051s latency).
Not shown: 65518 closed ports
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  loc-srv
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  unknown
5040/tcp  open  unknown
5985/tcp  open  unknown
47001/tcp open  unknown
49664/tcp open  unknown
49665/tcp open  unknown
49666/tcp open  unknown
49667/tcp open  unknown
49668/tcp open  unknown
49669/tcp open  unknown
49670/tcp open  unknown
49676/tcp open  unknown
51337/tcp open  unknown

Nmap scan report for 10.10.10.35
Host is up (0.0014s latency).
Not shown: 65529 filtered ports
PORT      STATE SERVICE
135/tcp   open  loc-srv
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  unknown
5040/tcp  open  unknown
49668/tcp open  unknown

Nmap scan report for 10.10.10.225
Host is up (0.00052s latency).
Not shown: 65510 closed ports
PORT      STATE SERVICE
53/tcp    open  domain
88/tcp    open  kerberos
135/tcp   open  loc-srv
139/tcp   open  netbios-ssn
389/tcp   open  ldap
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd
593/tcp   open  unknown
636/tcp   open  ldaps
3268/tcp  open  unknown
3269/tcp  open  unknown
3389/tcp  open  unknown
5985/tcp  open  unknown
9389/tcp  open  unknown
47001/tcp open  unknown
49664/tcp open  unknown
49665/tcp open  unknown
49667/tcp open  unknown
49668/tcp open  unknown
49669/tcp open  unknown
49672/tcp open  unknown
49674/tcp open  unknown
49693/tcp open  unknown
49700/tcp open  unknown
49706/tcp open  unknown

Nmap done: 256 IP addresses (5 hosts up) scanned in 38.68 seconds

```