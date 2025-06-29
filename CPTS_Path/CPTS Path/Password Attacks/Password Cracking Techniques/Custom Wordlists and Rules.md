![[Pasted image 20250628183830.png]]
- Knowing that users tend to keep their passwords as simple as possible, **we can create rules to generate likely weak passwords.**
- According to statistics provided by [WP Engine](https://wpengine.com/resources/passwords-unmasked-infographic/), **most passwords are no longer than `ten` characters.**
- One approach is to select familiar terms that are at least five characters long—such as **pet names, hobbies, personal preferences, or other common interests.**

## HashCat Rules Generation
---
![[Pasted image 20250628184151.png]]
![[Pasted image 20250628184204.png]]
- Generate the list:
	- `echo password > password.list`
	- `hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list`
	![[Pasted image 20250628184234.png]]
	- One of the most effective and widely used **==rulesets is `best64.rule`==**, which applies common transformations that frequently result in successful password guesses.
	
# Generating wordlists using CeWL
---
- We can use a tool called [CeWL](https://github.com/digininja/CeWL) to **scan potential words from a company's website and save them in a separate list.**
- We can then **combine** **this list with the desired rules to create a customized password list**—one that has a higher probability of containing the correct password for an employee.

	- `cewl https://www.inlanefreight.com -d 4 -m 6 --lowercase -w inlane.wordlist`
		- `-d`: Depth of the crawler
		- `-m`: Min length of the password
		- `--lowercase`: Lowercase passwords only
		- `-w`: output file

# Exercise
---
- For this sections exercise, **imagine that we compromised the password hash of a `work email` belonging to `Mark White`.** 
- After performing a bit of OSINT, we have gathered the following information about Mark:
	- ![[Pasted image 20250628185508.png]]
- The password hash is: `97268a8ae45ac7d15c3cea4ce6ea550b`.
- Crack the password

=> Make a list of known words:
	![[Pasted image 20250628193730.png]]

=> Combine these with themselves using hashcat combinator:
	`hashcat -a 1 password.list password.list --stdout > new_list`
	![[Pasted image 20250628193829.png]]

=> Make custom rules:
- ``
	![[Pasted image 20250628193903.png]]

=> Finally, generate the wordlist using the combined list and ruleset:
- `hashcat --force new_list -r custom_rules --stdout | sort -u > generated_list.txt`
- ![[Pasted image 20250628194030.png]]

=> Now crack the password:
- ![[Pasted image 20250628194101.png]]