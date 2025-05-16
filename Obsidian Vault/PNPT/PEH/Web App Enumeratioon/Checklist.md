Find behavior of the website with inputs
https://appsecexplained.gitbook.io/appsecexplained
https://github.com/swisskyrepo/PayloadsAllTheThings/

# SQL Injection
----
- Look on all the pages, **==also on where input can be stored like SignUp and Comments pages==**
- **Detection:**
	- `', ", ;` (single and double quotes) to trigger an error
	- `jeremy'`, `jeremy' OR 1=1#`(Logical Operators)
	- `-- -`, `#`, `--` (Comments)
- **Types:**
	- **==Union==**
		- [[UNION injection]]
		- `jeremy' UNION select NULL,NULL,table_name from information_schema.tables#`
	- ==**Blind Injection**==
		- [[BLIND injection part - 1]]
		- [[Lab - 2 Blind, Out of Band]]
		-  Pay **attention to ==`content-length` or `search` in response**== (in burp or browser).