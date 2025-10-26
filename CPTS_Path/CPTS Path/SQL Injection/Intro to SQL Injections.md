# Use of SQL in Web Applications
---
- First, let us see how web applications use databases MySQL, in this case, to store and retrieve data.
- For example, within a `PHP` web application, we can connect to our database, and start using the `MySQL` database through `MySQL` syntax, right within `PHP`, as follows:
	- ![[Pasted image 20251022213856.png]]
- Then, the **query's output will be stored in `$result`, and we can print it to the page or use it in any other way**. The below PHP code will print all returned results of the SQL query in new lines:
	- ![[Pasted image 20251022213912.png]]
- `If we use user-input within an SQL query, and if not securely coded, it may cause a variety of issues, like SQL Injection vulnerabilities.`

# SQL Injection
---
- An SQL injection **occurs when user-input is inputted into the SQL query string without properly sanitizing** or filtering the input.
- The previous example showed how user-input can be used within an SQL query, and **it did not use any form of input sanitization**:
- ![[Pasted image 20251022214244.png]]
- In typical cases, t**he `searchInput` would be inputted to complete the query, returning the expected outcome**.
- In this case, ==**we can add a single quote (`'`), which will end the user-input field, and after it, we can write actual SQL code**==. For example, if we search for `1'; DROP TABLE users;`, the search input would be:
	- `'%1'; DROP TABLE users;'`
- So, the final SQL query executed would be as follows:
	- `select * from logins where username like '%1'; DROP TABLE users;'`

- **Note**: In the above example, for the sake of simplicity, **we added another SQL query after a semi-colon (;).** 
- Though this is actually **not possible with MySQL,** it is **possible with MSSQL and PostgreSQL**. In the coming sections, we'll discuss the real methods of injecting SQL queries in MySQL.

# Syntax Errors
---
- The previous example of SQL injection would return an error:
	- `Error: near line 1: near "'": syntax error`
- This is because of the last trailing character, where **we have a single extra quote (`'`) that is not closed,** which causes a SQL syntax error when executed:
	- `select * from logins where username like '%1'; DROP TABLE users;'`
- To have a successful injection, **we must ensure that the newly modified SQL query is still valid and does not have any syntax errors** after our injection.
- **==One answer is by using `comments`,==** and we will discuss this in a later section. Another is to make the query syntax work by passing in multiple single quotes, as we will discuss next (`'`).

# Types of SQL Injections
---
![[Pasted image 20251022214641.png]]
![[Pasted image 20251022214721.png]]
