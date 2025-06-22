- Port: 3306
# Default Configuration
---
- `cat /etc/mysql/mysql.conf.d/mysqld.cnf`

# Dangerous Settings
---
![[Pasted image 20250616074009.png]]
- The settings user, password, and admin_address are **security-relevant** because the entries are **==made in plain text==**. Often, the rights for the configuration file of the MySQL server are not assigned correctly. 
- If we get **another way to read files or even a shell,** we can see the file and the username and password for the MySQL server.
- **The `debug` and `sql_warnings` settings provide verbose information** output in case of errors, which are essential for the administrator but should not be seen by others.

# Footprinting
---
- `nmap 10.129.14.128 -sV -sC -p3306 --script mysql*`
- `mysql -u root -h 10.129.14.132` => No password
- `mysql -u root -h 10.129.14.132 -p` => Prompt Password

-  The most important databases for the MySQL server are the **==system schema (sys) and information schema (information_schema).==**
- The system schema contains tables, information, and metadata necessary for management. More about this database can be found in the reference manual of MySQL.
- The `information schema` is also a database that contains metadata.