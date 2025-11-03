- The most common place **==we usually find LFI within is templating engines.==**
- In order to have **most of the static data same (`header`, `navigation bar`, and `footer`) and only some of the new data in multiple pages**, a **==templating engine dynamically loads other content that changes between pages==**. Otherwise, it would have to load the same static content in every page.
- This is why we often see a **==parameter like `/index.php?page=about`==**, where **`index.php` sets static content (e.g. header/footer)**, and then only pulls the **dynamic content specified in the parameter**, which in this case may be read **==from a file called `about.php`.==**
- As we have **control over the `about` portion of the request**, it may be possible to have the **==web application grab other files and display them==** on the page.
- LFI vulnerabilities can lead to **source code disclosure**, **sensitive data exposure**, and **even remote code execution** under certain conditions

# Examples of Vulnerable Code
---
- For example, the page **==may have a `?language` GET parameter,==** and if a user changes the language from a drop-down menu, then the same page would be returned but with a different `language` parameter (e.g. `?language=es`).

## PHP
- In `PHP`, we may **use the `include()` function** to load a local or a remote file as we load a page.
- If the `path` passed to the `include()` is taken from a user-controlled parameter, like a `GET` parameter, and `the code does not explicitly filter and sanitize the user input`, then the code becomes vulnerable to File Inclusion.
	- ![[Pasted image 20251031063839.png]]
- Any path **we pass in the `language` parameter will be loaded on the page,** including **any local files on the back-end server**.
- This is **not exclusive to the `include()` function**, as there are many other PHP functions that would lead to the same vulnerability.
- Such functions **include `include_once()`, `require()`, `require_once()`, `file_get_contents()`**, and several others as well.

## NodeJS
- ![[Pasted image 20251031064023.png]]
- As we can see, whatever parameter **passed from the URL gets used by the `readfile` function**, which then writes the file content in the HTTP response.

## ExpressJS
- ![[Pasted image 20251031064059.png]]
- Another example **is the `render()` function** in the `Express.js` framework.
- Unlike our earlier examples where GET parameters were specified after a (`?`) character in the URL, **==the above example takes the parameter from the URL path (e.g. `/about/en` or `/about/es`)==**

## Java
- ![[Pasted image 20251031064210.png]]
- The **`include` function may take a file or a page URL as its argument** and then **renders the object into the front-end template**, similar to the ones we saw earlier with NodeJS.
- The **`import` function may also be used to render a local file** **or a URL**, such as the following example:
	- `<c:import url= "<%= request.getParameter('language') %>"/>`

## .NET
- ![[Pasted image 20251031064322.png]]
- The **`Response.WriteFile` function works very similarly** to all of our earlier examples, as it **takes a file path for its input** and **writes its content to the response**. 
- The path may be retrieved from a GET parameter for dynamic content loading.
- ![[Pasted image 20251031064411.png]]

# Read vs Execute
---
- The most important thing to keep in mind is that **`some of the above functions only read the content of the specified files, while others also execute the specified files`**
- Furthermore, **some of them allow specifying remote URLs, while others only work with files local to the back-end server.**

|**Function**|**Read Content**|**Execute**|**Remote URL**|
|---|:-:|:-:|:-:|
|**PHP**||||
|`include()`/`include_once()`|✅|✅|✅|
|`require()`/`require_once()`|✅|✅|❌|
|`file_get_contents()`|✅|❌|✅|
|`fopen()`/`file()`|✅|❌|❌|
|**NodeJS**||||
|`fs.readFile()`|✅|❌|❌|
|`fs.sendFile()`|✅|❌|❌|
|`res.render()`|✅|✅|❌|
|**Java**||||
|`include`|✅|❌|❌|
|`import`|✅|✅|✅|
|**.NET**||||
|`@Html.Partial()`|✅|❌|❌|
|`@Html.RemotePartial()`|✅|❌|✅|
|`Response.WriteFile()`|✅|❌|❌|
|`include`|✅|✅|✅|
