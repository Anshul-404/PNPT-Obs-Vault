- Host was found as found in [[Network Sweep to find IPs]]

# Nmap Scan
---
```
Nmap scan report for 10.200.107.33
Host is up (0.18s latency).
Not shown: 65532 closed tcp ports (reset)
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 f5:97:39:2a:97:54:b1:5a:8b:c9:2f:9f:fc:49:23:bc (RSA)
|   256 8f:7e:01:05:77:6d:92:66:d8:a2:04:38:2d:b3:93:04 (ECDSA)
|_  256 5c:81:43:fe:65:ac:b3:0a:63:60:5f:4a:8d:e0:a3:2f (ED25519)
80/tcp    open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-generator: WordPress 5.5.3
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: holo.live
| http-robots.txt: 21 disallowed entries (15 shown)
| /var/www/wordpress/index.php 
| /var/www/wordpress/readme.html /var/www/wordpress/wp-activate.php 
| /var/www/wordpress/wp-blog-header.php /var/www/wordpress/wp-config.php 
| /var/www/wordpress/wp-content /var/www/wordpress/wp-includes 
| /var/www/wordpress/wp-load.php /var/www/wordpress/wp-mail.php 
| /var/www/wordpress/wp-signup.php /var/www/wordpress/xmlrpc.php 
| /var/www/wordpress/license.txt /var/www/wordpress/upgrade 
|_/var/www/wordpress/wp-admin /var/www/wordpress/wp-comments-post.php
33060/tcp open  mysqlx  MySQL X protocol listener
Device type: general purpose
Running: Linux 4.X
OS CPE: cpe:/o:linux:linux_kernel:4.15
OS details: Linux 4.15
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 3389/tcp)
HOP RTT       ADDRESS
1   162.18 ms 10.50.103.1
2   168.46 ms 10.200.107.33

```