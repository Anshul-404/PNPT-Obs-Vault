- PHP Wrappers allow us to **access different I/O streams at the application level, like standard input/output, file descriptors, and memory streams**.
- As web penetration testers, we can utilize **these wrappers to extend our exploitation attack**s and be able to **read PHP source code files** or even execute system commands.

# Input Filters
---
- [PHP Filters](https://www.php.net/manual/en/filters.php) are a type of **PHP wrapper**, where we can **pass different types of input and have it filtered by the filter we specify**. 
- To use PHP wrapper streams, **we can use the `php://` scheme in our string**, and we can **==access the PHP filter wrapper with `php://filter/`.==**

- The `filter` wrapper has several **parameters, but the main ones we require** for **==our attack are `resource` and `read`.==**
- The **==`resource` parameter is required for filter wrappers==**, and with it we can ==**specify the stream== we would like to apply the filter** on (e.g. a local file), ==**while the `read` parameter can apply different filters**== on the input resource, so we can use it to specify which filter we want to apply on our resource.

- There are four different types of filters available for use, **which are [String Filters](https://www.php.net/manual/en/filters.string.php), [Conversion Filters](https://www.php.net/manual/en/filters.convert.php), [Compression Filters](https://www.php.net/manual/en/filters.compression.php), and [Encryption Filters](https://www.php.net/manual/en/filters.encryption.php).** 
- You can read more about each filter on their respective link, but the filter that is useful for LFI attacks is the `convert.base64-encode` filter, under `Conversion Filters`.

# Fuzzing for PHP Files
---
- The first step would be to **fuzz for different available PHP pages** with a tool like `ffuf` or `gobuster`, as covered in the [Attacking Web Applications with Ffuf](https://academy.hackthebox.com/module/details/54) module:
	- ` ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://<SERVER_IP>:<PORT>/FUZZ.php`
	- ![[Pasted image 20251102012907.png]]

# Source Code Disclosure
---
- Let's try to **==read the source code of `config.php` using the base64 filter==**, by specifying **`convert.base64-encode` for the `read` parameter and `config` for the `resource` parameter,** as follows:
	- `php://filter/read=convert.base64-encode/resource=config`

**Note:** We **intentionally left the resource file** at the end of our string, as the **`.php` extension is automatically appended** to the end of our input string.
	![[Pasted image 20251102014256.png]]
- We can now decode the string using: `echo string | base64 -d` 