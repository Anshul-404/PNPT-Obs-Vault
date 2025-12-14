- Many organizations implement policies requiring users to change their passwords periodically to enhance security.
- However, these policies can inadvertently breed **predictable password patterns if users are not adequately educated** on proper password hygiene.
- For instance, a user might have an **initial password like "Summer2023"** and then, when prompted to update it, **==change it to "Summer2023!" or "Summer2024."==**
- Attackers capitalize on this human tendency by employing sophisticated techniques that combine the strengths of dictionary and brute-force attacks, drastically increasing the likelihood of successful password breaches.
# Hybrid Attacks in Action
---
Let's illustrate this with a practical example. Consider an attacker targeting an organization known to enforce regular password changes.
	![[Pasted image 20251021054627.png]]
- The attacker **begins by launching a dictionary attack**, using a wordlist curated with c**ommon passwords, industry-specific terms, and potentially personal information** related to the organization or its employees.
- However, if the dictionary attack proves u**nsuccessful, the hybrid attack seamlessly transitions** into a brute-force mode. 
- It strategically **==modifies the words from the original wordlist, appending numbers, special characters, or even incrementing years,==** as in our "Summer2023" example.

## The Power of Hybrid Attacks
---
- The effectiveness of hybrid attacks lies in their **adaptability and efficiency**. 
- They leverage the **strengths of both dictionary and brute-force techniques,** maximizing the chances of cracking passwords, especially in scenarios where users fall into predictable patterns.

- Let's consider a scenario where you have access to a common passwords wordlist, and you're targeting an organization with the following password policy:
	- Minimum length: 8 characters
	- Must include:
	    - At least one uppercase letter
	    - At least one lowercase letter
	    - At least one number

- Get the wordlist:
	- `wget https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/darkweb2017_top-10000.txt`
- Minimum length:
	- `grep -E '^.{8,}$' darkweb2017-top10000.txt > darkweb2017-minlength.txt`
- Minimum 1 Uppercase letter:
	- `grep -E '[A-Z]' darkweb2017-minlength.txt > darkweb2017-uppercase.txt`
- Minimum 1 Lowercase letter:
	- `grep -E '[a-z]' darkweb2017-uppercase.txt > darkweb2017-lowercase.txt`
- A digit:
	- `grep -E '[0-9]' darkweb2017-lowercase.txt > darkweb2017-number.txt`
# The Password Reuse Problem
---
The core issue fueling credential stuffing's success is the pervasive practice of password reuse. When users rely on the same or similar passwords for multiple accounts, a breach on one platform can have a domino effect, compromising numerous other accounts. This highlights the urgent need for strong, unique passwords for every online service, coupled with proactive security measures like multi-factor authentication.