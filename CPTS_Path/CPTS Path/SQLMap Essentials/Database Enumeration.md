# SQLMap Data Exfiltration
---
- For such purpose, **SQLMap has a predefined set of queries for all supported DBMSes**, where each entry **represents the SQL that must be run at the target to retrieve the desired content**.
- For example, **==if a user wants to retrieve the "banner" (switch `--banner`) for the target based on MySQL DBMS, the `VERSION()` query will be used for such purpose.==**
- In case of retrieval of the **current user name (switch `--current-user`), the `CURRENT_USER()**` query will be used.

## Basic DB Data Enumeration
---
- Usually, after a successful detection of an SQLi vulnerability, we can begin the enumeration of basic details from the database, **such as the hostname of the vulnerable target (`--hostname`), current user's name (`--current-user`), current database name (`--current-db`), or password hashes (`--passwords**`)
- Enumeration usually starts with the r**etrieval of the basic information:**
	- Database **version** banner (switch `--banner`)
	- Current **user name** (switch `--current-user`)
	- Current **database name** (switch `--current-db`)
	- Checking i**f the current user has DBA (administrator) rights** (switch `--is-dba`)

- The following SQLMap command does all of the above:
	- `sqlmap -u "http://www.example.com/?id=1" --banner --current-user --current-db --is-dba`

- **Note**: **The 'root' user in the database context in the vast majority of cases does not have any relation with the OS user "root",** other than that representing the privileged user within the DBMS context.
- This **basically means that the DB user should not have any constraints within the database context**, while OS privileges (e.g. file system writing to arbitrary location) should be minimalistic, at least in the recent deployments.
- The **same principle applies for the generic 'DBA' role.**

## Table Enumeration
---
- In most common scenarios, after finding the current database name (i.e. `testdb`), the **retrieval of table names would be by using the `--tables` option** and **specifying the DB name with `-D testdb`,** is as follows:
	- `sqlmap -u "http://www.example.com/?id=1" --tables -D testdb`
- After spotting the table name of interest, r**etrieval of its content can be done by using the `--dump` option** and specifying the **table name with `-T users**`, as follows:
	- `sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb`

# Table/Row Enumeration
---
- When dealing with large tables with many columns and/or rows, **we can specify the columns (e.g., only `name` and `surname` columns) with the `-C` option**, as follows:
	- `sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb -C name,surname`
- To narrow down the rows based on their ordinal number(s) inside the table, **we can specify the rows with the `--start` and `--stop` options** (e.g., start from 2nd up to 3rd entry), as follows:
	- `sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb --start=2 --stop=3`

## Conditional Enumeration
---
- If there is a requirement to retrieve certain rows based on a known `WHERE` condition (e.g. `name LIKE 'f%'`), **==we can use the option `--where`, as follows:==**
	- `sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb --where="name LIKE 'f%'"`

## Full DB Enumeration
---
- Instead of retrieving content per single-table basis, **we can retrieve all tables inside the database of interest by skipping the usage of option `-T` altogether (e.g. `--dump -D testdb`)**. 
- By simply **using the switch `--dump` without specifying a table with `-T`, all of the current database content will be retrieved.** 
- **As for the ==`--dump-all` switch==, all the content from ==all the databases== will be retrieved.**

- n such cases, a user is also advised to **include the switch `--exclude-sysdbs` (e.g. `--dump-all --exclude-sysdbs`)**, which will instruct **SQLMap to skip the retrieval of content from system databases**, as it is usually of little interest for pentesters.