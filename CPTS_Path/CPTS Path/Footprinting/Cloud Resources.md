- The use of cloud, such as [AWS](https://aws.amazon.com/), [GCP](https://cloud.google.com/), [Azure](https://azure.microsoft.com/en-us/), and others, is now one of the essential components for many companies nowadays. 
- After all, all companies want to be able to do their work from anywhere, so they need a central point for all management.

# Company Hosted Servers
---
- `for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done`

- Often cloud storage is added to the DNS list when used for administrative purposes by other employees. This step makes it much easier for the employees to reach and manage them.
- Let us stay with the case that a **company has contracted us, and during the IP lookup,** we have already seen that one IP **address belongs to the `s3-website-us-west-2.amazonaws.com`** server.


- However, there are many different ways to find such cloud storage. One of the easiest and most used is Google search combined with Google Dorks. **==For example, we can use the Google Dorks==** `inurl:` and `intext:` to narrow our search to specific terms.

![[Pasted image 20250615071307.png]]
- Here we can already see that the links presented by Google contain PDFs. When we search for a company that we may already know or want to know, **==we will also come across other files such as text documents, presentations, codes, and many others.==**
- Third-party providers such as [domain.glass](https://domain.glass) can also tell us a lot about the company's infrastructure.
# Private and Public SSH Keys Leaked
---
- Sometimes when employees are overworked or under high pressure, mistakes can be fatal for the entire company. 
- These errors can even lead to SSH private keys being leaked, which anyone can download and log onto one or even more machines in the company without using a password.