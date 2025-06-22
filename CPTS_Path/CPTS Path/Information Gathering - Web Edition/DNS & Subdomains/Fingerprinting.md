[[DNS]]
- Fingerprinting serves as a cornerstone of web reconnaissance for several reasons:
	- **Targeted Attacks**: By knowing the specific technologies in use, attackers can focus their efforts on exploits and vulnerabilities t**hat are known to affect those systems**. This significantly increases the chances of a successful compromise.
	- **Identifying Misconfigurations**: Fingerprinting can expose **misconfigured or outdated software, default settings,** or other weaknesses that might not be apparent through other reconnaissance methods.
	-  **Prioritising Targets**: When faced with multiple potential targets, **fingerprinting helps prioritise efforts by identifying systems more likely to be vulnerable** or hold valuable information.
	- **Building a Comprehensive Profile**: **Combining fingerprint data** with other reconnaissance findings creates **a holistic view of the target's infrastructure,** aiding in understanding its overall security posture and potential attack vectors.

# Fingerprinting Techniques
---
There are several techniques used for web server and technology fingerprinting:

- ==**`Banner Grabbing`==**: Banner grabbing involves analysing the banners presented by web servers and other services. These banners often reveal the server software, version numbers, and other details.
- ==**`Analysing HTTP Headers`**==`: HTTP headers transmitted with every web page request and response contain a wealth of information. The `Server` header typically discloses the web server software, while the `X-Powered-By` header might reveal additional technologies like scripting languages or frameworks.
- ==**`Probing for Specific Responses`**==: Sending specially crafted requests to the target can elicit unique responses that reveal specific technologies or versions. For example, certain error messages or behaviours are characteristic of particular web servers or software components.
- ==**`Analysing Page Content`**==: A web page's content, including its structure, scripts, and other elements, can often provide clues about the underlying technologies. There may be a copyright header that indicates specific software being used, for example.

# Tools
---
![[Pasted image 20250620075918.png]]
# Banner Grabbing
---
- `curl -I inlanefreight.com`

# Wafw00f
---
- `Web Application Firewalls` (`WAFs`) are security solutions designed to protect web applications from various attacks.
- To detect the presence of a WAF, we'll use the `wafw00f` tool. To install `wafw00f`, you can use pip3:
	- `pip3 install git+https://github.com/EnableSecurity/wafw00f --break-system-packages`

- `wafw00f inlanefreight.com`

## Nikto
---
- `Nikto` is a powerful open-source web server scanner. In addition to its primary function as a **vulnerability assessment tool,** `Nikto's` fingerprinting capabilities provide insights i**nto a website's technology stack.**
	- `nikto -h inlanefreight.com -Tuning b`
		- The `-Tuning b` flag tells `Nikto` to **only** run the **Software Identification modules.**