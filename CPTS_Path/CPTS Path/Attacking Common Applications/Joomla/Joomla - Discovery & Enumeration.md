- [Joomla](https://www.joomla.org/), released in August 2005 is another free and **open-source CMS used for discussion forums, photo galleries, e-Commerce, user-based communities**, and more.
- It is **==written in PHP and uses MySQL==** in the backend.
- . Like WordPress, Joomla can be enhanced with over **7,000 extensions and over 1,000 templates.**

## Discovery/Footprinting
---
- We can often fingerprint Joomla by looking at the page source, which tells us that we are dealing with a Joomla site:
	- `curl -s http://dev.inlanefreight.local/ | grep Joomla`
		- `    <meta name="generator" content="Joomla! - Open Source Content Management" />`
- The **==`robots.txt` file for a Joomla==** site will often look like this:
	- ![[Pasted image 20260714005007.png]]
- We can also often see the telltale Joomla favicon (but not always). We can fingerprint the **==Joomla version if the `README.txt` file is present.==*:
	- `curl -s http://dev.inlanefreight.local/README.txt | head -n 5`
	- ![[Pasted image 20260714005039.png]]
- In certain Joomla installs, **==we may be able to fingerprint the version from JavaScript files in the `media/system/js/` directory==** or by browsing to ==`administrator/manifests/files/joomla.xml`==.
	- `curl -s http://dev.inlanefreight.local/administrator/manifests/files/joomla.xml | xmllint --format -`
	- ![[Pasted image 20260714005129.png]]
- The **==`cache.xml` file can help to give us the approximate version==**. It is located at `plugins/system/cache/cache.xml`.

## Enumeration
---
- Let's try out **==[droopescan](https://github.com/droope/droopescan), a plugin-based scanner that works for SilverStripe, WordPress, and Drupal with limited functionality for Joomla and Moodle.==**
	- `droopescan -h`
		- ![[Pasted image 20260714010021.png]]
	
	- `droopescan scan joomla --url http://dev.inlanefreight.local/`
	- ![[Pasted image 20260714010135.png]]

- As we can see, it **==did not turn up much information aside from the possible version number==**. We can also try out [JoomlaScan](https://github.com/drego85/JoomlaScan), which is a Python tool inspired by the now-defunct OWASP [joomscan](https://github.com/OWASP/joomscan) tool.

- The **administrator login portal is located at ==`http://dev.inlanefreight.local/administrator/index.php`**.==
- Attempts at **user enumeration return a generic error message**:
	- `Warning Username and password do not match or you do not have an account yet.`

- The default **==administrator account on Joomla installs is `admin`==**, but the **==password is set at install time==**, so the only way we can hope to get into the admin back-end is if the **account is set with a very weak/common password** and we can get in with some guesswork or light brute-forcing.
- We can use this [script](https://github.com/ajnik/joomla-bruteforce) to **==attempt to brute force the login.==**
	- `sudo python3 joomla-brute.py -u http://dev.inlanefreight.local -w /usr/share/metasploit-framework/data/wordlists/http_default_pass.txt -usr admin`

# Q/A
---
- ![[Pasted image 20260714012624.png]]
- ![[Pasted image 20260714012634.png]]