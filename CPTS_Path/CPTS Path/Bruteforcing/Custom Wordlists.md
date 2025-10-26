# Username Anarchy
---
- Even when dealing with a seemingly simple name like "Jane Smith," m**anual username generation can quickly become a convoluted endeavor**. 
- While the **obvious combinations like jane, smith, janesmith, j.smith, or jane.s may seem adequate**, they **==barely scratch the surface==** of the potential username landscape.
- Jane could seamlessly weave in her middle name, **birth year, or a cherished hobby, leading to variations like `janemarie`, `smithj87`, or `jane_the_gardener`.**
- This is where **==`Username Anarchy` shines. It accounts for initials, common substitutions, and more, casting a wider net in your quest to uncover the target's username:==**:
	- `./username-anarchy -l`
	- ![[Pasted image 20251021183930.png]]

## Installation and Usage
----
- `sudo apt install ruby -y`
- `git clone https://github.com/urbanadventurer/username-anarchy.git`
- `cd username-anarchy`
- **Usage**: `./username-anarchy Jane Smith > jane_smith_usernames.txt`

# CUPP
---
- `CUPP` **(Common User Passwords Profiler)** is a tool designed to **create highly personalized password wordlists that leverage the gathered intelligence about your target.**
- So, where can one gather this valuable intelligence for a target like Jane Smith?
	- `Social Media`: A goldmine of **personal details: birthdays, pet names, favorite quotes, travel destinations, significant others, and more**. Platforms like Facebook, Twitter, Instagram, and LinkedIn can reveal much information.
	- `Company Websites`: Jane's **current or past employers' websites** might list her name, **position, and even her professional bio, offering** insights into her work life.
	- `Public Records`: Depending on jurisdiction and **privacy laws, public records might divulge details about Jane's address, family members,** property ownership, or even past legal entanglements.
	- `News Articles and Blogs`: Has Jane been featured in **any news articles or blog posts**? These could shed light on her interests, achievements, or affiliations.
- ![[Pasted image 20251021184744.png]]
- CUPP will then **take your inputs and create a comprehensive list of potential passwords:**
	- Original and Capitalized: `jane`, `Jane`
	- Reversed Strings: `enaj`, `enaJ`
	- Birthdate Variations: `jane1994`, `smith2708`
	- Concatenations: `janesmith`, `smithjane`
	- Appending Special Characters: `jane!`, `smith@`
	- Appending Numbers: `jane123`, `smith2024`
	- Leetspeak Substitutions: `j4n3`, `5m1th`
	- Combined Mutations: `Jane1994!`, `smith2708@`

## Installation and Usage:
---
- `sudo apt install cupp -y`
- `cupp -i`
- ![[Pasted image 20251021184857.png]]
- As we did earlier, we can use **grep to filter that password list to match that policy:**
	- `grep -E '^.{6,}$' jane.txt | grep -E '[A-Z]' | grep -E '[a-z]' | grep -E '[0-9]' | grep -E '([!@#$%^&*].*){2,}' > jane-filtered.txt`
- Bruteforce:
	- `hydra -L usernames.txt -P jane-filtered.txt IP -s PORT -f http-post-form "/:username=^USER^&password=^PASS^:Invalid credentials"`