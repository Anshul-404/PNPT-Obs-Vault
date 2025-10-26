# Comments
---
- We can use **two types of line comments with MySQL `--` and `#`, in addition to an in-line comment `/**/`** (though this is not usually used in SQL injections). The `--` can be used as follows:
	- `SELECT username FROM logins; -- Selects usernames from the logins table `
- **Note**: In SQL, using **two dashes only is not enough to start a comment.** So, **==there has to be an empty space after them, so the comment starts with (-- ), with a space at the end==**. 
- This is sometimes URL encoded as (--+), as spaces in URLs are encoded as (+). To make it clear, we will add another (-) at the end (-- -), to show the use of a space character.

- The **`#` symbol can be used as well**:
	- `SELECT * FROM logins WHERE username = 'admin'; # You can place anything here AND password = 'something'`
- Tip: if you are inputting your payload in the URL within a browser, **a (#) symbol is usually considered as a tag, and will not be passed as part of the URL.** In order to **==use (#) as a comment within a browser, we can use '%23', which is an URL encoded (#) symbol.==**

# Auth Bypass with comments
---
- `SELECT * FROM logins WHERE username='admin'-- ' AND password = 'something';`
- The **username is now `admin`, and the remainder of the query is now ignored** as a comment. Also, this way, we can ensure that the query does not have any syntax issues.

## Another Example
- Expressions within the **parenthesis take precedence over other operators and are evaluated first**. Let us look at a scenario like this:
	- ![[Pasted image 20251023064313.png]]
- So let us try logging in with the credentials of another user, such as `tom`.
	- ![[Pasted image 20251023064404.png]]
- Logging in as the user **with an id not equal to 1 was successful.**
- We know from the previous section on comments that we can use them to **==comment out the rest of the query.==** So, let us try using `admin'--` as the username.
- ![[Pasted image 20251023064443.png]]
- The final query as a result of our input is:
	- `SELECT * FROM logins where (username='admin')`
	- 