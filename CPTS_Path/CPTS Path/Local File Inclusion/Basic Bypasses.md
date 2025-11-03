# Non-Recursive Path Traversal Filters
---
- One of the most basic filters against LFI is a search and replace filter, where it **==simply deletes substrings of (`../`) to avoid path traversals==**. For example:
	
	- `$language = str_replace('../', '', $_GET['language']);`

- The above code is supposed to prevent path traversal, and hence renders LFI useless. It replaces instances of `../` from URL with empty string.
- We see that all `../` substrings were removed, which resulted in a **==final path being `./languages/etc/passwd`.==**
- However, this filter is **very insecure, as it is ==NOT `recursively removing` the `../` substring**==
- For example, **==if we use `....//` as our payload,==** then the filter would **remove `../` and the output string would be `../`,** which means we may still perform path traversal.

- The `....//` substring is not the only bypass we can use, as we may use:
	- `..././` 
	-  `....\/` and several other recursive LFI payloads.
	  
- Furthermore, in some cases, **escaping the forward slash character may also work to avoid path traversal filters** **(e.g. `....\/`),** or **adding extra forward slashes (e.g. `....////`)**

# Encoding
---
- If the target **==web application did not allow `.` and `/` in our input, we can URL encode `../` into `%2e%2e%2f`==**, which may bypass the filter. 
- Core **==PHP filters on versions 5.3.4 and earlier were specifically vulnerable==** to this bypass. But even on **==newer versions we may find custom filters that may be bypassed through URL encoding.==**
- To do so, we can use any online URL encoder utility or use the Burp Suite Decoder tool, as follows:
	- ![[Pasted image 20251101062208.png]]

- **Note:** For this to work **we must URL encode all characters, including the dots**. Some **URL encoders may not encode dots as they are considered to be part of the URL** scheme.
- Furthermore, we may also use **==Burp Decoder to encode the encoded string once again to have a `double encoded` string==**, which **may also bypass other types of filters.**

# Approved Paths
---
- Some web applications may also use **Regular Expressions to ensure** that the **==file being included is under a specific path.==**
- For example, the web application we have been dealing with may **only accept paths that are under the `./languages` directory**, as follows:

```php
if(preg_match('/^\.\/languages\/.+$/', $_GET['language'])) {
    include($_GET['language']);
} else {
    echo 'Illegal path specified!';
}
```

- To find the approved path, **we can examine the requests sent by the existing forms,** and see **what path they use for the normal web functionality.**
- Furthermore, w**e can fuzz web directories under the same path,** and try ==different ones until we get a match.==
- To bypass this, we may use path traversal and **==start our payload with the approved path, and then use `../` to go back==** to the root directory
- ![[Pasted image 20251101063126.png]]
- Some web applications **may apply this filter along with one of the earlier filters, so we may combine both techniques** by starting our payload with the approved path, and then URL encode our payload or use recursive payload.

# Appended Extension
---
- With modern versions of PHP, we may not be able to bypass this and will be restricted to only reading files in that extension, which may still be useful.
- There are a couple of other techniques we may use, but they are **==`obsolete with modern versions of PHP and only work with PHP versions before 5.3/5.4`.==**

## Path Truncation
- In earlier versions of PHP, **defined strings have a maximum length of 4096 characters, likely due to the limitation of 32-bit systems**.
- If a ==**longer string is passed, it will simply be `truncated`,**== and any **characters** **after the maximum length will be ignored**.
- PHP also **used to remove trailing slashes and single dots in path names**, so if we call **==(`/etc/passwd/.`) then the `/.` would also be truncated, and PHP would call (`/etc/passwd`).==**
- **PHP, and Linux systems in general, also disregard multiple slashes** in the path ==(e.g. `////etc/passwd` is the same as `/etc/passwd`).==
- Similarly, a current **directory shortcut (`.`) in the middle of the path would also be disregarded (e.g. `/etc/./passwd`).**

- If we combine both of these PHP limitations together, **we can create very long strings that evaluate to a correct path.**
- Whenever **==we reach the 4096 character limitation, the appended extension (`.php`) would be truncated,==** and we would have a path without an appended extension.
- Finally, it is also important to note that we would also need to **==`start the path with a non-existing directory` for this technique to work.==**
- An example of such payload would be the following:
	- `?language=non_existing_directory/../../../etc/passwd/./././././ REPEATED ~2048 times]`

- Automation for writing 2048 times:
	- `echo -n "non_existing_directory/../../../etc/passwd/" && for i in {1..2048}; do echo -n "./"; done`

- We **may also increase the count of `../`, as adding more would still land us in the root directory**, as explained in the previous section.
- However, if we use this method, **==we should calculate the full length of the string to ensure only `.php` gets truncated and not our requested file==** at the end of the string (`/etc/passwd`).

# Null Bytes
---
- **==PHP versions before 5.5 were vulnerable to `null byte injection`==**, which means that adding a **null byte (`%00`) at the end of the string would terminate the string** and not consider anything after it.
- To exploit this vulnerability, we **can end our payload with a null byte (e.g. `/etc/passwd%00`), such that the final path passed to `include()` would be (`/etc/passwd%00.php`)**