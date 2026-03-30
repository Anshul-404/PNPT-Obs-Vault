- While **programmers mainly conside**r the two most commonly used HTTP methods, **==`GET` and `POST`==**, any client can **==send any other methods in their HTTP requests==** and then see how the web server handles these methods.
- If the web server configurations are **not restricted to only accept the HTTP methods** required by the web server (e.g. `GET`/`POST`), and the web **application is not developed to handle other types of HTTP** requests (e.g. `HEAD`, `PUT`), then **==we may be able to exploit this insecure configuration to gain access to functionalities we do not have access to,==** or even bypass certain security controls.


# HTTP Verb Tampering
---
- To understand `HTTP Verb Tampering`, we must first learn about the different methods accepted by the HTTP protocol. HTTP has [9 different verbs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) that can be accepted as HTTP methods by web servers. Other than `GET` and `POST`, the following are some of the commonly used HTTP verbs:

| Verb      | Description                                                                                         |
| --------- | --------------------------------------------------------------------------------------------------- |
| `HEAD`    | Identical to a GET request, but its response only contains the `headers`, without the response body |
| `PUT`     | Writes the request payload to the specified location                                                |
| `DELETE`  | Deletes the resource at the specified location                                                      |
| `OPTIONS` | Shows different options accepted by a web server, like accepted HTTP verbs                          |
| `PATCH`   | Apply partial modifications to the resource at the specified location                               |
- As you can imagine, some of the above methods **==can perform very sensitive functionalities, like writing (`PUT`) or deleting (`DELETE`) files==** to the webroot directory on the back-end server. 

## Insecure Configurations
---
- A web server's **authentication configuration may be limited to specific HTTP methods**, which **==would leave some HTTP methods accessible without authentication.==**
	- For example:- Developer may write code to **require authentication for GET AND POST:**
		- ![[Pasted image 20260329012705.png]]
	- But, the other methods like **==HEAD and PUT would bypass this as they don't require authentication.==**
- As we can see, even though the configuration specifies both `GET` and `POST` requests for the authentication method, an attacker may still use a different HTTP method (like `HEAD`) to bypass this authentication mechanism altogether.
- This eventually leads to an authentication bypass and allows attackers to **access web pages and domains they should not have access to.**

## Insecure Coding
---- 
- This can occur when a web developer **==applies specific filters to mitigate particular vulnerabilities while not covering all HTTP methods==** with that filter.
- For example, if a web page was found to be **vulnerable to a SQL Injection vulnerability**, and the back-end developer **mitigated** the SQL Injection vulnerability by the following **==applying input sanitization filters on GET parameter:==**
	- ```php
	  $pattern = "/^[A-Za-z\s]+$/";
		if(preg_match($pattern, $_GET["code"])) {
		    $query = "Select * from ports where port_code like '%" . $_REQUEST["code"] . "%'";
		    ...SNIP...
		}
	  ```
- If the **GET requests do not contain any bad characters, then the query would be executed.**
- However, when the query is executed, **==the `$_REQUEST["code"]` parameters are being used, which may also contain `POST` parameters==**, `leading to an inconsistency in the use of HTTP Verbs`.
- In this case, an attacker **==may use a `POST` request to perform SQL injection,==** in which case the `GET` parameters would be empty (will not include any bad characters). The request would pass the security filter, which would make the function still vulnerable to SQL Injection.