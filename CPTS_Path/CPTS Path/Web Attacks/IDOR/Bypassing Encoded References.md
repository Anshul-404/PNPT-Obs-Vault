	- In the previous section, we saw an example of an IDOR that uses employee uids in clear text, making it easy to enumerate. In some cases, web applications **make hashes or encode their object references, making enumeration more difficult,** but it may still be possible.
- Let's go back to the `Employee Manager` web application to test the `Contracts` functionality:
	- ![[Pasted image 20260406012340.png]]
- If we click on the `Employment_contract.pdf` file, it starts downloading the file. The intercepted request in Burp looks as follows:
	- ![[Pasted image 20260406012359.png]]
- We see that it is sending a `POST` request to `download.php` with the following data:
	- `contract=cdd96d3cc73d1dbdaffa03cc6cd7339b`

- Using a **==`download.php` script to download files is a common practice to avoid directly linking to files,==** as that may be exploitable with multiple web attacks.
- In this case, the web application is not sending the direct reference in cleartext but appears **to be hashing it in an `md5` format.** Hashes are one-way functions, so we cannot decode them to see their original values.
- We can attempt to **==hash various values, like `uid`, `username`, `filename`, and many others, and see if any of their `md5` hashes match==** the above value. 
- If we find a match, then we can replicate it for other users and collect their files. For example, **==let's try to compare the `md5` hash of our `uid`==**, and see if it matches the above hash:
	- `c4ca4238a0b923820dcc509a6f75849b -`

- Unfortunately, the hashes do not match. We can attempt this with various other fields, **==but none of them matches our hash.==** In advanced cases, we may also utilize `Burp Comparer` and fuzz various values and then compare each to our hash to see if we find any matches. 
- In this case, **==the `md5` hash could be for a unique value or a combination of values,==** which would be very difficult to predict, **making this direct reference a `Secure Direct Object Reference`**. However, there's one fatal flaw in this web application.

## Function Disclosure
---
- As most modern web applications are developed using JavaScript frameworks, like `Angular`, `React`, or `Vue.js`, many web developers may make the mistake of **performing sensitive functions on the front-end,** which would expose them to attackers.
- For example, if the above **==hash was being calculated on the front-end, we can study the function and then replicate==** what it's doing to calculate the same hash. Luckily for us, this is precisely the case in this web application.

- If we take a look at the link in the source code, we see that it is calling a **JavaScript function with `javascript:downloadContract('1')`**. Looking at the `downloadContract()` **function in the source code, we see the following:**
```php
	function downloadContract(uid) {
	    $.redirect("/download.php", {
	        contract: CryptoJS.MD5(btoa(uid)).toString(),
	    }, "POST", "_self");
	}
```

- This function appears to be sending a `POST` request with the `contract` parameter, which is what we saw above. 
- The value it is sending is an `md5` hash using the `CryptoJS` library, which also matches the request we saw earlier. So, the only thing left to see is what value is being hashed.
- In this case, the value being hashed is **==`btoa(uid)`, which is the `base64` encoded string of the `uid` variable,==** which is an input argument for the function. 
- Going back to the earlier link where the function was called, we see it calling `downloadContract('1')`. So, the final value being used in the `POST` request is the **`base64` encoded string of `1`, which was then `md5` hashed.**
	- ![[Pasted image 20260406013452.png]]
- **Tip:** We are using **==the `-n` flag with `echo`, and the `-w 0` flag with `base64`, to avoid adding newlines==**, in order to be able to calculate the `md5` hash of the same value, without hashing newlines, as that would change the final `md5` hash.

## Mass Enumeration
---
- Once again, let us write a **simple bash script to retrieve all employee contracts.** More often than not, this is the easiest and most efficient method of enumerating data and files through IDOR vulnerabilities. 
- In more advanced cases, we may utilize tools like `Burp Intruder` or `ZAP Fuzzer`, but a simple bash script should be the best course for our exercise.

- We can start by calculating the hash for each of the first ten employees using the same previous command while using `tr -d` to remove the trailing `-` characters, as follows:
	- `for i in {1..10}; do echo -n $i | base64 -w 0 | md5sum | tr -d ' -'; done`
- Next, we can make a **`POST` request on `download.php` with each of the above hashes as the `contract` value,** which should give us our final script:
```bash
	for i in {1..10}; do
	    for hash in $(echo -n $i | base64 -w 0 | md5sum | tr -d ' -'); do
	        curl -sOJ -X POST -d "contract=$hash" http://SERVER_IP:PORT/download.php
	    done
	done
```


## Question
---
Q> Try to download the contracts of the first 20 employee, one of which should contain the flag, which you can read with 'cat'. You can either calculate the 'contract' parameter value, or calculate the '.pdf' file name directly.

A> base64encode(uid) -> urlencode

![[Pasted image 20260406020350.png]]

- In Burpsuite:
	- ![[Pasted image 20260406020730.png]]