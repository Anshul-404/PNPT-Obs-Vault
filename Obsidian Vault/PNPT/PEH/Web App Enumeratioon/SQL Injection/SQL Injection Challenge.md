![[Pasted image 20250406034038.png]]
- **Search parameter can be injectable, let's see**
- Giving `invalid_product` gives `No Products found`:
	- ![[Pasted image 20250406035253.png]]
- **If injection is successful,** **we should not receive** `No products found`
- ![[Pasted image 20250406035352.png]]
- ==**Works, but it is blind SQL injection**==

# Extracting Details
---
- Number of columns being selected: 4
	- ![[Pasted image 20250406035546.png]]
- Name of database: `peh-labs`
	- ![[Pasted image 20250406041436.png]]
	- ![[Pasted image 20250406041505.png]]

- Name of tables given in `video` for easy manual injection:
	- ![[Pasted image 20250406042149.png]]
- Column names: `username` and `password`
	- `product=invalid_product' UNION SELECT NULL,NULL,NULL,column_name from information_schema.columns where table_name='injection0x03_users' AND table_schema='peh-labs' AND column_name='password' LIMIT 1#`
	- ![[Pasted image 20250406043315.png]]
	- ![[Pasted image 20250406043349.png]]
- Username: `takeshi`
	- ![[Pasted image 20250406044616.png]]
- Password: `onigirigadaisuki`	
	- ![[Pasted image 20250406044803.png]]