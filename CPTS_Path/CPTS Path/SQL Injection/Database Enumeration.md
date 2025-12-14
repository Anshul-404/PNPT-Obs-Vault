# MySQL Fingerprinting
---
- MySQL-
	- ![[Pasted image 20251024063144.png]]
- The output `10.3.22-MariaDB-1ubuntu1` means that **we are dealing with a `MariaDB` DBMS similar to MySQL.**
  
# INFORMATION_SCHEMA Database
---
- To pull data from tables using `UNION SELECT`, we need to properly form our `SELECT` queries. To do so, we need the following information:
	
	- List of **databases**
	- List of **tables within each database**
	- List of **columns** within each table
- The **==[INFORMATION_SCHEMA](https://dev.mysql.com/doc/refman/8.0/en/information-schema-introduction.html) database contains metadata about the databases and tables present on the server.==**
- This database plays a crucial role while exploiting SQL injection vulnerabilities.
- If we only specify **a table's name for a `SELECT` statement, it will look for tables within the same database.**
- So, **==to reference a table present in another DB, we can use the dot ‘`.`’ operator.==**
	- `SELECT * FROM my_database.users;`
- Similarly, **we can look at ==tables present in the `INFORMATION_SCHEMA` Database

## SCHEMATA
---
- To start our enumeration, we should **find what databases are available on the DBMS.**
- The **==table [SCHEMATA](https://dev.mysql.com/doc/refman/8.0/en/information-schema-schemata-table.html) in the `INFORMATION_SCHEMA` database contains information about all databases on the server.==**
- The ==**`SCHEMA_NAME` column contains all the database names currently present.**==
  
	- `SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;`
	- ![[Pasted image 20251024063705.png]]
- We can **==find the current database with the `SELECT database()` query==**. 

## TABLES
---
- To **find all tables within a database, we can ==use the `TABLES` table in the `INFORMATION_SCHEMA` Database==.**
- The [TABLES](https://dev.mysql.com/doc/refman/8.0/en/information-schema-tables-table.html) table **contains information about all tables** throughout the database.
- The **==`TABLE_NAME` column stores table names, while the `TABLE_SCHEMA` column points to the database==** each table belongs to.
	- `select TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.TABLES`
- If we know the database name, we can simply add where clause:
	- `WHERE TABLE_SCHEMA='database_name'`

# COLUMNS
---
- The **==[COLUMNS](https://dev.mysql.com/doc/refman/8.0/en/information-schema-columns-table.html) table in INFORMATION_SCHEMA contains information about all columns==** present in all the databases.
- The **`COLUMN_NAME`, `TABLE_NAME`, and `TABLE_SCHEMA` columns** can be used to achieve this.

	- `select COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'`

## Data
---
- Now that we have all the information, **we can form our `UNION` query to dump data of the `username` and `password` columns** from the `credentials` table in the `dev` database. 
