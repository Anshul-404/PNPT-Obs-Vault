- [WordPress](https://wordpress.org/), launched in 2003, is an open-source Content Management System (CMS) that can be used for multiple purposes. It’s often used to host blogs and forums.
- However, its **customizability and extensible nature make it prone to vulnerabilities through third-party themes** and plugins.
- The [Hacking WordPress](https://academy.hackthebox.com/course/preview/hacking-wordpress) module on HTB Academy goes very far in-depth on the structure and function of WordPress and ways it can be abused.

# Discovery/Footprinting
---
- A quick way to identify a WordPress site is **==by browsing to the `/robots.txt` file.==** 
- A typical robots.txt on a WordPress installation may look like:

```
	User-agent: *
	Disallow: /wp-admin/
	Allow: /wp-admin/admin-ajax.php
	Disallow: /wp-content/uploads/wpforms/
	
	Sitemap: https://inlanefreight.local/wp-sitemap.xml
```

- Here the **presence of the `/wp-admin` and `/wp-content` directories would be a dead giveaway that we are dealing with WordPress.** 
- Typically **attempting to browse to the `wp-admin` directory will redirect us to the `wp-login.php` page.** This is the login portal to the WordPress instance's back-end.
  
- WordPress stores its **==plugins in the `wp-content/plugins` directory. This folder is helpful to enumerate vulnerable plugins.==** 
- **==Themes are stored in the `wp-content/themes` directory. These files should be carefully enumerated as they may lead to RCE.==**

- There are five types of users on a standard WordPress installation.
	1. **Administrator**: This user has access to **administrative features within the website.** This includes adding and deleting users and posts, as well as editing source code.
	2. **Editor: An editor can publish and manage posts, including the posts of other users.**
	3. **Author: They can publish and manage their own posts.**
	4. Contributor: These users can write and manage their own posts but cannot publish them.
	5. Subscriber: These are standard users who can browse posts and edit their profiles.
1. Getting access to an administrator is usually sufficient to obtain code execution on the server. 
2. **==Editors and authors might have access to certain vulnerable plugins,==** which normal users don’t.

# Enumeration
---
- Another quick way to identify a WordPress site is by looking at the page source. 
- Viewing the **page with `cURL` and grepping for `WordPress` can help us confirm that WordPress is in use and footprint the version number**, which we should note down for later. We can enumerate WordPress using a variety of manual and automated tactics.
	- `curl -s http://blog.inlanefreight.local | grep WordPress`
	- ```<meta name="generator" content="WordPress 5.8" /```

- **Browsing the site and perusing the page source** will give us hints to the **==theme in use, plugins installed, and even usernames if author names are published with posts==**. 
- We should **==spend some time manually browsing the site and looking through the page source for each page==**, grepping for the `wp-content` directory, `themes` and `plugin`, and begin building a list of interesting data points.

- Looking at the page source, **we can see that the [Business Gravity](https://wordpress.org/themes/business-gravity/) theme is in use.** 
- We can go further and **attempt to fingerprint the theme version number and ==look for any known vulnerabilities that affect it.==**

	- `curl -s http://blog.inlanefreight.local/ | grep themes`
	- `<link rel='stylesheet' id='bootstrap-css'  href='http://blog.inlanefreight.local/wp-content/themes/business-gravity/assets/vendors/bootstrap/css/bootstrap.min.css' type='text/css' media='all' />`

- Next, let's take a look at w**hich plugins we can uncover.**
	- `curl -s http://blog.inlanefreight.local/ | grep plugins`
	- 
	```
		<link rel='stylesheet' id='contact-form-7-css'  href='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/css/styles.css?ver=5.4.2' type='text/css' media='all' />
		<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/subscriber.js?ver=5.8' id='subscriber-js-js'></script>
		<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/jquery.validationEngine-en.js?ver=5.8' id='validation-engine-en-js'></script>
		<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/jquery.validationEngine.js?ver=5.8' id='validation-engine-js'></script>
		        <link rel='stylesheet' id='mm_frontend-css'  href='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/css/mm_frontend.css?ver=5.8' type='text/css' media='all' />
		<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/js/index.js?ver=5.4.2' id='contact-form-7-js'></script>
	```
- From the output above, we know that the **[Contact Form 7](https://wordpress.org/plugins/contact-form-7/) and [mail-masta](https://wordpress.org/plugins/mail-masta/) plugins are installed**. 
- The next step would be **==enumerating the versions==**.

- Browsing to **`http://blog.inlanefreight.local/wp-content/plugins/mail-masta/` shows us that directory listing** is enabled and **==that a `readme.txt` file is present.==**
- From the readme, it appears **that version 1.0.0 of the plugin is installed, which suffers from a [Local File Inclusion](https://www.exploit-db.com/exploits/50226) vulnerability** that was published in August of 2021.

- Let's dig around a bit more. Checking the page source of another page, **we can see that the [wpDiscuz](https://wpdiscuz.com/) plugin is installed, and it appears to be version 7.0.4**:
	- `curl -s http://blog.inlanefreight.local/?p=1 | grep plugins`
	- ```
		<link rel='stylesheet' id='contact-form-7-css'  href='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/css/styles.css?ver=5.4.2' type='text/css' media='all' />
		<link rel='stylesheet' id='wpdiscuz-frontend-css-css'  href='http://blog.inlanefreight.local/wp-content/plugins/wpdiscuz/themes/default/style.css?ver=7.0.4' type='text/css' media='all' />
		```
- A quick search for this plugin version shows [this](https://www.exploit-db.com/exploits/49967) **unauthenticated remote code execution vulnerability from June of 2021.**
- It is important at this stage to **==not jump ahead of ourselves and start exploiting the first possible flaw we see, as there are many other potential vulnerabilities and misconfigurations possible==** in WordPress that we don't want to miss.


# Enumerating Users
---
- We can do some manual enumeration of users as well. As mentioned earlier, the d**efault WordPress login page can be found at `/wp-login.php`.**
- A **==valid username and an invalid password results==** in the following message:
	- ![[Pasted image 20260511003336.png]]
- However, an ==**invalid username returns that the user was not found**==:
	- ![[Pasted image 20260511003358.png]]

- This makes WordPress **==vulnerable to username enumeration, which can be used to obtain a list of potential usernames.==**

- Let's recap. At this stage, we have gathered the following data points:
	- The site appears to be running WordPress core version 5.8
	- The installed theme is Business Gravity
	- The following plugins are in use: Contact Form 7, mail-masta, wpDiscuz
	- The wpDiscuz version appears to be 7.0.4, which suffers from an unauthenticated remote code execution vulnerability
	- The mail-masta version seems to be 1.0.0, which suffers from a Local File Inclusion vulnerability
	- The WordPress site is vulnerable to user enumeration, and the user `admin` is confirmed to be a valid user\

# WPScan
---
- [WPScan](https://github.com/wpscanteam/wpscan) is an **==automated WordPress scanner and enumeration tool. It determines if the various themes and plugins used by a blog are outdated or vulnerable==**. It’s installed by default on Parrot OS but can also be installed manually with `gem`.
	- $`sudo gem install wpscan`

- WPScan is **also able to pull in vulnerability information from external sources.** 
- We can **==obtain an API token from [WPVulnDB](https://wpvulndb.com/), which is used by WPScan to scan for PoC and reports.==** 
- The free plan allows up to 25 requests per day. To use the WPVulnDB database, just create an account and copy the API token from the users page. This token can then be supplied to wpscan using the `--api-token parameter`.

- The **`--enumerate` flag is used to enumerate various components of the WordPress application,** such as plugins, themes, and users.
- **By default, WPScan enumerates vulnerable plugins, themes, users, media, and backups.** 
- However, s**pecific arguments can be supplied to restrict enumeration to specific components**. 
- For example, **==all plugins can be enumerated using the arguments `--enumerate ap`==**. 
- Let’s invoke a n**ormal enumeration scan against a WordPress website with the `--enumerate` flag and pass it an API token from WPVulnDB with the `--api-token` flag.**
		- `sudo wpscan --url http://blog.inlanefreight.local --enumerate --api-token dEOFB<SNIP>`


- WPScan uses **==various passive and active methods to determine versions and vulnerabilities, as shown in the report above.==** 
- The default number of threads used is `5`. However, this value can be changed using the `-t` flag.
- This scan helped us confirm some of the things we uncovered from manual enumeration (WordPress core version 5.8 and directory listing enabled), showed us that **the theme that we identified was not exactly correct (Transport Gravity is in use which is a child theme of Business Gravity),** uncovered **==another username (john)==**, and showed that automated enumeration on its own is often not enough (missed the wpDiscuz and Contact Form 7 plugins).

- WPScan provides **==information about known vulnerabilities.==** The report output **==also contains URLs to PoCs, which would allow us to exploit these vulnerabilities.==**


- Scanners are **==great and are very useful but cannot replace the human touch and a curious mind==**. Honing our enumeration skills can set us apart from the crowd as excellent penetration testers.


# Q/A
---
- Run wpscan:
	- `wpscan --url http://blog.inlanefreight.local/ --api-token=MY_KEY`
		- **==Always Read Interesting Findings before going through exploits:==**
	- ![[Pasted image 20260511013624.png]]

- **==Go through each url/comment, everything to manually identify plugins as==**:
	- `curl http://blog.inlanefreight.local/?p=1#comment-1 | grep plugin`
- Try using **==readme.txt for each plugin==** to identify versions:
	- `http://blog.inlanefreight.local/wp-content/plugins/wp-sitemap-page/readme.txt`