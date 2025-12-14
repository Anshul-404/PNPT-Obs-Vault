# Curl Commands
---
- One of the best and easiest ways to properly set up an SQLMap request against the specific target (i.e., web request with parameters inside) is by utilizing **`Copy as cURL` feature from within the Network (Monitor) panel inside the Chrome, Edge, or Firefox Developer Tools:*
- By pasting the clipboard content (`Ctrl-V`) into the command line, and changing the original command `curl` to `sqlmap`, we are able to use SQLMap with the identical `curl` command:
	- `sqlmap 'http://www.example.com/?id=1' -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:80.0) Gecko/20100101 Firefox/80.0' -H 'Accept: image/webp,*/*' -H 'Accept-Language: en-US,en;q=0.5' --compressed -H 'Connection: keep-alive' -H 'DNT: 1`

# GET/POST Requests
---
- In the most common scenario, **`GET` parameters are provided with the usage of option `-u`/`--url`**, as in the previous example.
- As for **testing `POST` data, the `--data` flag** can be used, as follows:
	- `sqlmap 'http://www.example.com/' --data 'uid=1&name=test'`
- In such cases, **`POST` parameters `uid` and `name` will be tested for SQLi vulnerability**
- If we have a **clear indication that the parameter `uid` is prone** to an SQLi vulnerability, ==**we could narrow down the tests to only this parameter** **using `-p uid`.**==
- Otherwise, **we could mark it inside the provided data with the usage of special** marker `*` as follows:
	- `sqlmap 'http://www.example.com/' --data 'uid=1*&name=test'`

# Full HTTP Requests
---
- If we need to specify a **complex HTTP request** with lots of different header values and an **==elongated POST body, we can use the `-r` flag.==**
- With this option, **SQLMap is ==provided with the "request file,"**== containing the **whole HTTP request inside a single textual file**
- In a common scenario, such HTTP request can be captured from within a specialized proxy application (e.g. `Burp`) and written into the request file.
	- `sqlmap -r req.txt`

- Tip: **similarly to the case with the '--data' option,** within the saved request file, **`we can specify the parameter we want to inject in with an asterisk (*), such as '/?id=*'.`**

# Custom SQLMap Requests
---
- If we wanted to craft complicated requests manually, there are numerous switches and options to fine-tune SQLMap.
- For example, if there is a **requirement to specify the (session) cookie value to `PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c` option `--cookie`** would be used as follows:
	- `sqlmap ... --cookie='PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'`
- The **same effect** can be done with the **usage of option `-H/--header`:**
	- `sqlmap ... -H='Cookie:PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'`
- We can apply the same to options like `--host`, `--referer`, and `-A/--user-agent`, which are used to specify the same HTTP headers' values.\

- Furthermore, t**here is a switch ==`--random-agent==` designed to randomly select a `User-agent` header value** from the included database of regular browser values.
- This is an **important switch to remember,** as more and more protection **solutions automatically ==drop all HTTP traffic containing the recognizable== default SQLMap's User-agent value (e.g. `User-agent: sqlmap/1.4.9.12#dev (http://sqlmap.org)`).** Alternatively, **the `--mobile` switch can be used to imitate** the smartphone by using that same header value.

- While SQLMap, **by default, targets only the HTTP parameters,** it is possible to **==test the headers for the SQLi vulnerability.==**
- The easiest way is to **==specify the "custom" injection mark after the header's value (e.g. `--cookie="id=1*"`).==**

- Also, if we wanted to **specify an alternative HTTP method, other than `GET` and `POST`** (e.g., `PUT`), we can utilize the option `--method`, as follows:
	- `sqlmap -u www.target.com --data='id=1' --method PUT`

# Custom HTTP Requests
----
- Apart from the most common form-data `POST` body style (e.g. `id=1`), **SQLMap also supports JSON formatted (e.g. `{"id":1}`)** **and XML formatted (e.g. `<element><id>1</id></element>`)** HTTP requests.
- Support for these formats is implemented in a "relaxed" manner; thus, there **are no strict constraints on how the parameter values are stored inside.** 
- In case the `POST` body is relatively simple and short, **the option `--data` will suffice.**
- However, **in the case of a complex or long POST body, we can once again use the `-r` option:**
	- ![[Pasted image 20251026063238.png]]
- 