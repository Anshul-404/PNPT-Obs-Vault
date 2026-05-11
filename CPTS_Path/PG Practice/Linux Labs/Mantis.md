# Scope
----
- `192.168.161.204`

# Enumeration
----
- Nmap scan:

```
Nmap scan report for 192.168.156.204
Host is up (0.077s latency).
Not shown: 65533 filtered tcp ports (no-response)
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Slick - Bootstrap 4 Template
3306/tcp open  mysql   MariaDB 5.5.5-10.3.34
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.3.34-MariaDB-0ubuntu0.20.04.1
|   Thread ID: 17
|   Capabilities flags: 63486
|   Some Capabilities: Support41Auth, LongColumnFlag, Speaks41ProtocolOld, ODBCClient, SupportsTransactions, Speaks41ProtocolNew, SupportsCompression, SupportsLoadDataLocal, IgnoreSigpipes, InteractiveClient, DontAllowDatabaseTableColumn, FoundRows, IgnoreSpaceBeforeParenthesis, ConnectWithDatabase, SupportsAuthPlugins, SupportsMultipleStatments, SupportsMultipleResults
|   Status: Autocommit
|   Salt: :IBOEL.@9=bZzwh$tA'>
|_  Auth Plugin Name: mysql_native_password

```

## Port 80
---
- Run gobuster to reveal Mantis Bugtracker running:
```
/css                  (Status: 301) [Size: 316] [--> http://192.168.161.204/css/]
/license.txt          (Status: 200) [Size: 548]
/js                   (Status: 301) [Size: 315] [--> http://192.168.161.204/js/]
/fonts                (Status: 301) [Size: 318] [--> http://192.168.161.204/fonts/]
/bugtracker           (Status: 301) [Size: 323] [--> http://192.168.161.204/bugtracker/]
Progress: 173652 / 882236 (19.68%)^C
```
![[Pasted image 20260503002125.png]]

- Notice the "**==Warning==**"
- **Not able to find any version in source code, so unable to determine direct exploits.**
- Let's bruteforce the `/bugtracker` directory:
	- ![[Pasted image 20260503002252.png]]
- A few important directories:
	- `admin`, `config`, `scripts`
- Config directory gives these files:
	- ![[Pasted image 20260503002427.png]]
	- ![[Pasted image 20260503002500.png]]
	- We found **==username and database name, but we are not able to login with mysql with empty password==**:
		- ![[Pasted image 20260503002543.png]]
	- Running more gobuster in `admin` directory:
		- ![[Pasted image 20260503003138.png]]
	- Checking `install.php` (only this one opens):
		- ![[Pasted image 20260503003430.png]]
# Vulnerability
---
- After searching a lot of time, we find `CVE-2017-12419` : Arbitrary file read:
	- If, after successful installation of MantisBT through 2.5.2 on MySQL/MariaDB, **==the administrator does not remove the 'admin' directory (as recommended==** in the "Post-installation and upgrade tasks" section of the MantisBT Admin Guide), and the **==MySQL client has a local_infile setting enabled (in php.ini mysqli.allow_local_infile, or the MySQL client config file==**, depending on the PHP setup), **==an attacker may take advantage of MySQL's "connect file read" feature to remotely access files on the MantisBT server.==**

- Download `rogue-mysql-server` to host mysql server:
	- The **script starts a MySQL server that requests and retrieves files from clients that connect to it.**
	- `https://github.com/jib1337/Rogue-MySQL-Server/blob/master/RogueSQL.py`
		- ![[Pasted image 20260503004136.png]]
	- 