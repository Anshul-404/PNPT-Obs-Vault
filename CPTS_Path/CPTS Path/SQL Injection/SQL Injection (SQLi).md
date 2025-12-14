- Many types of injection vulnerabilities are possible within web applications, such as **HTTP injection, code injection, and command injection**. The most common example, however, is SQL injection. 
- A SQL injection occurs when a **malicious user attempts to pass input that changes the final SQL query sent by the web application to the database**, enabling the user to perform other unintended SQL queries directly against the database.
- In the **most basic case**, this is done by **injecting a single quote (`'`) or a double quote (`"`) to escape the limits of user input and inject data directly into the SQL query.**

# Use Cases and Impact
---
- A SQL injection can have a tremendous impact, especially if privileges on the back-end server and database are very lax.
- First, **we may ==retrieve secret/sensitive information== that should not be visible to us, like user logins and passwords or credit card information,** which can then be used for other malicious purposes.
- Another use case of SQL injection is to **subvert the intended web application logic.** The most common example of this is **==bypassing login== without passing a valid pair of username and password credentials.**
- Attackers may also be able to **read and write files directly on the back-end server**, which may, in turn, lead to **placing back doors on the back-end server**, and gaining direct control over it, and **==eventually taking control over the entire website.==**

# Connect to MySQL
---
- `mysql -h 94.237.57.211 -P 48572 -u root -p --skip-ssl`