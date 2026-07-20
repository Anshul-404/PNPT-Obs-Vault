Splunk is a log analytics tool used to gather, analyze and visualize data. Though not originally intended to be a SIEM tool, Splunk is often used for security monitoring and business analytics.
Splunk deployments are often used to **==house sensitive data and could provide a wealth of information==** for an attacker if compromised.

# Discovery/Footprinting
---
- Splunk is prevalent in internal networks and **==often runs as root on Linux or SYSTEM==** on Windows systems.
- The Splunk web server **==runs by default on port 8000.==** 
- On older versions of Splunk, **==the default credentials are `admin:changeme`==**, which are conveniently displayed on the login page.
	- ![[Pasted image 20260721012653.png]]
- If the default credentials do not work, it is worth checking for common weak passwords such as `admin`, `Welcome`, `Welcome1`, `Password123`, etc.

- Here we can see that Nmap identified the **==`Splunkd httpd` service on port 8000 and port 8089, the Splunk management port== for communication with the Splunk REST API.**
	![[Pasted image 20260721012802.png]]

# Enumeration
---
- The Splunk Enterprise trial converts to a **==free version after 60 days, which doesn’t require authentication.==**
- It is not uncommon for system administrators to install a trial of Splunk to test it out, which is subsequently forgotten about.
- This will **==automatically convert to the free version that does not have any form of authentication, introducing a security hole==** in the environment
	- ![[Pasted image 20260721012911.png]]

- Once logged in to Splunk (or having accessed an instance of Splunk Free), we can **browse data, run reports, create dashboards, install applications from the Splunkbase library, and install custom applications**.
	- ![[Pasted image 20260721012929.png]]

- Splunk has multiple ways of running code, such as server-side Django applications, REST endpoints, scripted inputs, and alerting scripts. 
- A **==common method of gaining remote code execution on a Splunk server is through the use of a scripted input.==** These are designed to help integrate Splunk with data sources such as APIs or file servers that require custom methods to access. Scripted inputs are intended to run these scripts, with STDOUT provided as input to Splunk.

- As Splunk can be installed on Windows or Linux hosts, **==scripted inputs can be created to run Bash, PowerShell, or Batch scripts.==**
- Also, **==every Splunk installation comes with Python installed, so Python scripts can be run on any Splunk system.==** A quick way to gain RCE is by creating a scripted input that tells Splunk to run a Python reverse shell script. We'll cover this in the next section.
	- At the time of writing, Splunk has [47](https://www.cvedetails.com/vulnerability-list/vendor_id-10963/Splunk.html) CVEs.