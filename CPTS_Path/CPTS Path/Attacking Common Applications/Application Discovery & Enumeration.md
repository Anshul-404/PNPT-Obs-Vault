- As penetration testers, we need to have **strong enumeration skills and be able to get the "lay of the land" on any network starting** with very little to no information (black box discovery or just a set of CIDR ranges). Typically, when we connect to a network, **we'll start with a ping sweep to identify "live hosts."**
- From there, **==we will usually begin targeted port scanning and, eventually, deeper port scanning to identify running services==**. 
- In a network with hundreds or thousands of hosts, this enumeration data can become unwieldy. 
- Let's say we perform an Nmap port scan to **identify common web services such as**:


## Nmap - Web Discovery
---
- `nmap -p 80,443,8000,8080,8180,8888,10000 --open -oA web_discovery -iL scope_list`
- We **may find an enormous amount of hosts with services running** on ports 80 and 443 alone.
	- Lucky for us, several great tools exist that can greatly assist in this process. 
	- Two phenomenal tools that **==every tester should have in their arsenal are [EyeWitness](https://github.com/FortyNorthSecurity/EyeWitness) and [Aquatone](https://github.com/michenriksen/aquatone).==** 
	- Both of these tools can be **fed raw Nmap XML scan output** (Aquatone can also take Masscan XML; EyeWitness can take Nessus XML output) and **==be used to quickly inspect all hosts running web applications and take screenshots of each.==**

- These **screenshots can help us narrow down potentially 100s of hosts and build a more targeted list of applications** that we should spend more time enumerating and attacking.

## Getting Organized
---
- An **example OneNote (also applicable to other tools) structure may look like the following for the discovery phase**:
- `External Penetration Test - <Client Name>`
	- `Scope` (including in-scope I**P addresses/ranges, URLs, any fragile hosts, testing timeframes, and any limitations or other relative information** we need handy)
	- `Client Points of Contact`
	- `Credentials`
	- `Discovery/Enumeration`
	    - `Scans`
	    - `Live hosts`
	- `Application Discovery`
	    - `Scans`
	    - `Interesting/Notable Hosts`
	- `Exploitation`
	    - `<Hostname or IP>`
	    - `<Hostname or IP>`
	- `Post-Exploitation`
	    - `<Hostname or IP>`
	    - `<Hostname or IP>`

## Initial Enumeration
---
- Let's assume our client provided us with the following scope:

```
DrAsstrange@htb[/htb]$ cat scope_list 
	app.inlanefreight.local
	dev.inlanefreight.local
	drupal-dev.inlanefreight.local
	drupal-qa.inlanefreight.local
	drupal-acc.inlanefreight.local
	drupal.inlanefreight.local
	blog-dev.inlanefreight.local
	blog.inlanefreight.local
	app-dev.inlanefreight.local
	jenkins-dev.inlanefreight.local
	jenkins.inlanefreight.local
	web01.inlanefreight.local
	gitlab-dev.inlanefreight.local
	gitlab.inlanefreight.local
	support-dev.inlanefreight.local
	support.inlanefreight.local
	inlanefreight.local
	10.129.201.50
```

- While reviewing the screenshot report of the most common ports, **I may run a more thorough Nmap scan against the top 10,000 ports or all TCP ports**, depending on the size of the scope. 
- Since enumeration is an iterative process, we will **run a ==web screenshotting tool against any subsequent Nmap scans we perform to ensure maximum coverage.**==
- Let's dig into the scope list mentioned above with an **Nmap scan that will typically discover most web applications in an environmen**t. We will, of course, perform deeper scans later on, but this will give us a good starting point.

- **==Note==**: Not all hosts in the scope list above will be accessible when spawning the target below. There will be separate, similar, exercises at the end of this section in order to reproduce much of what is shown here.

![[Pasted image 20260509011015.png]]

- From the results, we can infer that **one of the hosts is Windows and the remainder are Linux** (but cannot be 100% certain at this stage).
- In this lab, we are utilizing Vhosts to simulate the subdomains of a company. **==Hosts with `dev` as part of the FQDN are worth noting down as they may be running untested features==** or have things like debug mode enabled.
- We would also want to **add `gitlab-dev.inlanefreight.local` to our "interesting hosts" list to dig into once we complete the discovery phase**. 
- We may be able to **==access public Git repos that could contain sensitive information==** such as credentials or clues that may lead us to other subdomains/Vhosts.
- It is not uncommon to find Gitlab instances that allow us to register a user without requiring admin approval to activate the account. We may find additional repos after logging in. 
- It would also be worth **checking previous commits for data such as credentials** which we will cover more in detail later in this module when we dig deeper into Gitlab.

- Enumerating **==one of the hosts further using an Nmap service scan (`-sV`) against the default top 1,000 ports==** can tell us more about what is running on the webserver.
	- ![[Pasted image 20260509011234.png]]
	- From the output above, we can see that an IIS web server is running on the default port 80, and it appears that `Splunk` is running on port 8000/8089, while `PRTG Network Monitor` is present on port 8080.

## Using EyeWitness
---
- EyeWitness can take the **XML output from both Nmap and Nessus and create a report with screenshots of each web application** present on the various ports using Selenium.
- It will also take things a step further and **==categorize the applications where possible, fingerprint them, and suggest default credentials==** based on the application. It can also be given a list of IP addresses and URLs and be told to pre-pend `http://` and `https://` to the front of each.
- It will perform DNS resolution for IPs and can be given a specific set of ports to attempt to connect to and screenshot.
		$`sudo apt install eyewitness`

- Let's run the **default `--web` option to take screenshots using the Nmap XML output** from the discovery scan as input:
	- `eyewitness --web -x web_discovery.xml -d inlanefreight_eyewitness`
		- ![[Pasted image 20260509011628.png]]

## Using Aquatone
---
- [Aquatone](https://github.com/michenriksen/aquatone), as mentioned before, is similar to EyeWitness and **can take screenshots when provided a `.txt` file of hosts or an Nmap `.xml` file with the `-nmap` flag.**
- We can compile Aquatone on our own or download a precompiled binary. After downloading the binary, we just need to extract it, and we are ready to go:
		$`wget https://github.com/michenriksen/aquatone/releases/download/v1.7.0/aquatone_linux_amd64_1.7.0.zip`
		$`unzip aquatone_linux_amd64_1.7.0.zip`

- We can move it to a location in our `$PATH` such as `/usr/local/bin` to be able to call the tool from anywhere or just drop the binary in our working (say, scans) directory.
- In this example, **we provide the tool the same `web_discovery.xml` Nmap output specifying the `-nmap` flag,** and we're off to the races:
	- `cat web_discovery.xml | ./aquatone -nmap`
	- ![[Pasted image 20260509011819.png]]

## Interpreting the Results
--- 
- After opening the report, **==we see that the report is organized into categories, with `High Value Targets` being first and typically the most "juicy" hosts==** to go after.
- In the below report, I would be **immediately excited to see Tomcat on any assessment** (but especially during an External Penetration Test) and would **==try default credentials on the `/manager` and `/host-manager` endpoints.==** 
- If we can access either, we can upload a **malicious WAR file and achieve remote code execution** on the underlying host using [JSP code](https://en.wikipedia.org/wiki/Jakarta_Server_Pages). More on this later in the module.
		![[Pasted image 20260509012118.png]]

- **==Custom web applications are always worth testing as they may contain a wide variety of vulnerabilities==**. Here I would also be interested to see if the website was running a popular CMS such as WordPress, Joomla, or Drupal.
- The next application, `http://support-dev.inlanefreight.local`, is interesting because it **appears to be running [osTicket](https://osticket.com/), which has suffered from various severe vulnerabilities over the years.**
- Support ticketing systems are of **==particular interest because we may be able to log in and gain access to sensitive information.==**

- This last piece was demonstrated in the **HTB weekly release box [Delivery](https://0xdf.gitlab.io/2021/05/22/htb-delivery.html) by [IppSec](https://www.youtube.com/watch?v=gbs43E71mFM). This particular box is worth studying as it shows what is possible by exploring the built-in functionality of certain common applications.** We will cover osTicket more in-depth later in this module.
		![[Pasted image 20260509012257.png]]


- During an assessment, I would **continue reviewing the report, noting down interesting hosts, including the URL and application name/version for later**. It is important at this point to **==remember that we are still in the information gathering phase, and every little detail could make or break our assessment.==**
- We **==should not get careless and begin attacking hosts right away, as we may end up down a rabbit hole and miss something crucial later==** in the report. During an External Penetration Test, I would expect to see a mix of custom applications, some CMS, perhaps applications such as Tomcat, Jenkins, and Splunk, remote access portals such as Remote Desktop Services (RDS), SSL VPN endpoints, Outlook Web Access (OWA), O365, perhaps some sort of edge network device login page, etc.

# Q/A
----
- Nmap scan:
	- `nmap 10.129.42.195 -iL scope_list.txt -oX nmap_output.xml `
	- ![[Pasted image 20260509014044.png]]
- Start eyewitness:
	- `eyewitness --web -x nmap_output.xml -d inlanefreight_eyewitness`
	- ![[Pasted image 20260509014144.png]]

- Running aquatone:
	- `cat nmap_output.xml | ./aquatone -nmap`