- Before we start executing entire SQL queries, we will first **learn to modify the original query by injecting the `OR` operator and using SQL comments to subvert the original query's logic.**

## Authentication Bypass
---
- The page also displays the SQL query being executed to understand better how we will subvert the query logic. 
- Our **goal is to log in as the admin user without using the existing password**. As we can see, the current SQL query being executed is:
	- `SELECT * FROM logins WHERE username='admin' AND password = 'p@ssw0rd';`
	- ![[Pasted image 20251023052302.png]]

## SQLi Discovery
---
- To test SQL Injection, we will try to ==**add one of the below payloads after our username** and see **if it causes any errors or changes how the page behaves**:==
	- ![[Pasted image 20251023052348.png]]
- **Note**: In some cases, **we may have to use the URL encoded version of the payload**. An **example of this is when we put our payload directly in the URL** 'i.e. HTTP GET request'.
- So, let us **start by injecting a single quote**:
	- ![[Pasted image 20251023052511.png]]
- As discussed in the previous section, **the quote we entered resulted in an odd number of quotes**, causing a syntax error.

## OR Injection
---
- We would need the **==query always to return `true`==**, regardless of the username and password entered, **to bypass the authentication.**
- As previously discussed, the MySQL documentation for [operation precedence](https://dev.mysql.com/doc/refman/8.0/en/operator-precedence.html) **==states that the `AND` operator would be evaluated before the `OR`==** operator.
- This means that if there is **at least one `TRUE` condition in the entire query along with an `OR` operator, the entire query will evaluate to `TRUE`** since the `OR` operator returns `TRUE` if one of its operands is `TRUE`.
- An example of a condition that **==will always return `true` is `'1'='1'`.==** 
- However, to keep the SQL query working and keep an even number of quotes, instead of using ('1'='1'), **we will remove the last quote and use ('1'='1),** so the **remaining single quote from the original query would be in its place.**
- So, if we inject the below condition and have an `OR` operator between it and the original condition, it should always return `true`:
	- `admin' or '1'='1`
- The final query should be as follow:
	- `SELECT * FROM logins WHERE username='admin' or '1'='1' AND password = 'something';`
- ![[Pasted image 20251023052838.png]]
- ![[Pasted image 20251023053111.png]]![[Pasted image 20251023052910.png]]
- Note: The payload we used above is one of many auth bypass payloads we can use to subvert the authentication logic. 
	- You can find a **==comprehensive list of SQLi auth bypass payloads in [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection#authentication-bypass)==**, each of which works on a certain type of SQL queries.

# Auth Bypass with OR operator
---
- We were able to log in successfully as admin. 
- However, **what if we did not know a valid username**? Let us try the **same request with a different username this time.**
- ![[Pasted image 20251023053540.png]]
- ![[Pasted image 20251023053708.png]]
- To successfully log in once again, **we will need an overall `true` query.** 
- This can be achieved by **==injecting an `OR` condition into the password field, so it will always return `true`==**. Let us try `something' or '1'='1` as the password.
- ![[Pasted image 20251023053815.png]]
- The **additional `OR` condition resulted in a `true` query overall**, as the `WHERE` clause **==returns everything in the table==**, and the **==user present in the first row is logged in.==**
- In this case, as **both conditions will return `true`**, we do not have to provide a test username and password and can directly start with the `'` injection and log in with just `' or '1' = '1`.