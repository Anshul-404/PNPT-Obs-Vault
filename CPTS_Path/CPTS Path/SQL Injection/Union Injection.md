# Union
---
- Before we start learning about Union Injection, we should first learn more about the SQL Union clause. 
- The **Union clause is used to combine results from multiple SELECT statements**. 
- This means that through a UNION injection, **we will be able to SELECT and dump data from all across the DBMS, from multiple tables and databases**.
	- `SELECT * FROM ports UNION SELECT * FROM ships;`
- **==Note: The data types of the selected columns on all positions should be the same.==**

# Even Columns
---
- A `UNION` statement can **==ONLY operate on `SELECT` statements with an equal number of columns.==** 
- For example, if we attempt to `UNION` two queries that have results with a different number of columns, we get the following error:
	- ![[Pasted image 20251023070035.png]]
- For example, if the query is:
	- `SELECT * FROM products WHERE product_id = 'user_input'`
- We can inject a `UNION` query into the input, such that rows from another table are returned:
	- `SELECT * from products where product_id = '1' UNION SELECT username, password from passwords-- '`

## Un-even Columns
---
- We will find out that the original query will **usually not have the same number of columns as the SQL query we want to execute**, so we will have to work around that.
- In that case, we want to **==`SELECT`, we can put junk data for the remaining required columns so that the total number of columns we are `UNION`ing with remains the same as the original query.==**
- If we `UNION` with the string `"junk"`, the `SELECT` query would be 
	- `SELECT "junk" from passwords`, 
- which will always return `junk`. 
- We can also use numbers. For example, the query `SELECT 1 from passwords` will always return `1` as the output.
- Tip: **For advanced SQL injection, we may want to simply use 'NULL' to fill other columns, as =='NULL' fits all data types.**==
	- `SELECT * from products where product_id = '1' UNION SELECT username, 2 from passwords`
- If we had more columns in the table of the original query, we have to add more numbers to create the remaining required columns. 
- For example, **if the original query used `SELECT` on a table with four columns, our `UNION` injection would be:**
	- `UNION SELECT username, 2, 3, 4 from passwords-- '`

# Union Injection
---
## Detect number of columns

- Before going ahead and exploiting Union-based queries, we need to find the number of columns selected by the server. T**here are two methods of detecting the number of columns**:
	- Using ORDER BY
	- Using UNION

#### Using ORDER BY
---
- We have to inject a query that **sorts the results by a column we specified, 'i.e., column 1, column 2, and so on', until we get an error saying the column specified does not exist.**
- For example, ==**we can start with `order by 1`, sort by the first column==, and succeed, as the table must have at least one column.**
	- Then we will do `order by 2` and then `order by 3` **==until we reach a number that returns an error, or the page does not show any output, which means that this column number does not exist.==**
- If we failed **at `order by 4`, this means the table has three columns,** which is the number of columns we were able to sort by successfully:
	- `' order by 1-- -`
	- `' ORDER BY 5-- -`

- Note:- In SQL, **`ORDER BY 1` is a shorthand method to ==sort the result set based on the first column== in the `SELECT` statement's projection.** 
- The **==number `1` refers to the ordinal position of the column==** in the `SELECT` list, **==NOT the column name itself==**.

#### Using UNION
---
- The other method is to **attempt a Union injection with a different number of columns until we successfully get the results back.**
- The **first method always returns the results until we hit an error**, while **this method always gives an error until we get a success**.
	- `cn' UNION select 1,2,3,4-- -`

## Location of Injection
---
- It is **very common that not every column will be displayed back to the user**. For example, **==the ID field is often used to link different tables together, but the user doesn't need to see it.==**
- ==`We cannot place our injection at the beginning, or its output will not be printed.`==

- This is the benefit of **using numbers as our junk data,** as it makes it **easy to track which columns are printed, so we know at which column to place our query.**:
	- `cn' UNION select 1,@@version,3,4-- -`